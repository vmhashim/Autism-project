 ## Introduction
   In today's world, social interactions and emotional understanding are crucial for personal and professional success. However, many individuals with Autism Spectrum Disorder (ASD) face significant challenges in recognizing and interpreting facial expressions, making these interactions more difficult. This project aims to address these challenges by developing an innovative, user-friendly solution tailored to the needs of those with ASD. Leveraging advanced technologies such as augmented reality , we aim to create engaging and personalized tools that provide real-time feedback and immersive learning experiences. By enhancing the ability to recognize emotions, this project seeks to improve social connectivity and overall well-being for individuals with ASD, helping them navigate the complexities of today's social landscape more effectively.

 ## Overview
   In today's world, individuals with Autism Spectrum Disorder (ASD) often struggle with recognizing facial expressions, which impacts their social interactions and emotional development. Existing tools often lack engagement and personalized support. This project addresses these challenges by developing a Smart Interactive Device using Arduino Uno. The device utilizes RFID cards to display emotions, which are read and processed for visual and tactile feedback. Stress relief smiley balls with sensors provide interactive and calming feedback. Integrated into educational and therapeutic settings, the device facilitates learning through games and activities designed to improve facial expression recognition and enhance social interaction skills, aiming to improve emotional intelligence and overall well-being for individuals with ASD.
- **Arduino Uno Microcontroller**: Central processing unit for device operations.
- **RFID Cards**: Display emotions and interact with the system for recognition.
- **Stress Relief Smiley Balls**: Provide tactile feedback and interaction.
- **Buzzer and LED Indicators**: Offer auditory and visual cues for feedback.
- **Push Buttons**: Allow users to select emotions and interact with the device.
- **JQ6500 MP3 Voice Sound Module**: Enhances learning with audio cues.
- **Monitor**: Displays emotions and feedback to the user.

# Components Required

| Item Name                    | Quantity | Description           | Links to Product |
|------------------------------|----------|-----------------------|------------------|
| Arduino Uno Microcontroller  | 1        | Central processor     |                  |
| RFID Cards                   | 4        | Emotion display       |                  |
| RFID Reader                  | 1        | Reads RFID cards      |                  |
| Buzzer                       | 1        | Auditory feedback     |                  |
| LED Indicators               | 1        | Visual feedback       |                  |
| Push Buttons                 | 4        | User interaction      |                  |
| OLD Display                  | 1        | Displays emotions     |                  |


# Pin Connections

| Component           | Arduino Uno Pin | Description                    |
|---------------------|-----------------|--------------------------------|
| RFID Reader (SPI)   | 10 (SS), 9 (RST)| Communicates with RFID cards    |
| Buzzer              | 1               | Provides auditory feedback      |
| LED Indicators      | 2 (red), 3 (green) | Provides visual feedback     |
| Push Buttons        | 4, 5, 6, 7, 8   | Interacts with user for emotion selection |

## Working Code
```

#include <SPI.h>
#include <MFRC522.h>

// Define the pins used by the RFID reader
#define RST_PIN 9
#define SS_PIN 10

// Create an instance of the MFRC522 library
MFRC522 rfid(SS_PIN, RST_PIN);

// Define the RFID to string mapping
const int numMappings = 5;
const byte mappings[numMappings][4] = {
  {0x5A, 0xF0, 0x0B, 0xB0},
  {0x1C, 0x0F, 0x33, 0x49},
  {0x53, 0x77, 0x08, 0x50},
  {0x53, 0xF9, 0x4A, 0xAC},
  {0xDE, 0x5C, 0xAE, 0x1D}
};
const char* strings[numMappings] = {"happy", "sad", "fear", "angry", "neutral"};

const int button1Pin = 8;
const int button2Pin = 7;
const int button3Pin = 6;
const int button4Pin = 5;
const int button5Pin = 4;
const int greenLedPin = 3;
const int redLedPin = 2;
const int buzzerPin = 1;

String userInput = "";

void setup() {
  Serial.begin(9600);
  SPI.begin();
  rfid.PCD_Init();

  pinMode(button1Pin, INPUT_PULLUP);
  pinMode(button2Pin, INPUT_PULLUP);
  pinMode(button3Pin, INPUT_PULLUP);
  pinMode(button4Pin, INPUT_PULLUP);
  pinMode(button5Pin, INPUT_PULLUP);
  pinMode(greenLedPin, OUTPUT);
  pinMode(redLedPin, OUTPUT);
  pinMode(buzzerPin, OUTPUT);

  digitalWrite(greenLedPin, LOW);
  digitalWrite(redLedPin, LOW);
  digitalWrite(buzzerPin, LOW);
}

void playNote(int noteFrequency, int duration) {
  tone(buzzerPin, noteFrequency, duration);
  delay(duration + 50);
  noTone(buzzerPin);
}

void loop() {
  // Check if a new RFID card is available
  if (rfid.PICC_IsNewCardPresent()) {
    // Verify if the NUID has been readed
    if (rfid.PICC_ReadCardSerial()) {
      // Check if the RFID UID matches any of the mappings
      bool found = false;
      for (int i = 0; i < numMappings; i++) {
        if (memcmp(rfid.uid.uidByte, mappings[i], sizeof(mappings[i])) == 0) {
          userInput = strings[i];
          found = true;
          break;
        }
      }

      // If no matching m apping is found, print a message
      if (!found) {
        Serial.println("Unknown RFID UID");
        userInput = "";
      }

      // Halt the PICC (RFID tag) to make it inactive
      rfid.PICC_HaltA();
    }
  }

  if (!userInput.equals("")) {
    Serial.print("Press a RFID card of emotion '" + userInput + "': ");

    bool greenLedOn = false;
    bool redLedOn = false;

    while (true) {
      if (digitalRead(button1Pin) == LOW) {
        Serial.println("Button pressed: 1");
        if (userInput.equalsIgnoreCase("happy")) {
          Serial.println("Matched: " + userInput);
          greenLedOn = true;
          playNote(400, 500); // Play a higher frequency for "happy"
        } else {
          Serial.println("Not matched");
          redLedOn = true;
          playNote(150, 500);
        }
        delay(1000);
        break;
      } else if (digitalRead(button2Pin) == LOW) {
        Serial.println("Button pressed: 2");
        if (userInput.equalsIgnoreCase("sad")) {
          Serial.println("Matched: " + userInput);
          greenLedOn = true;
          playNote(294, 500);
        } else {
          Serial.println("Not matched");
          redLedOn = true;
          playNote(175, 500);
        }
        delay(1000);
        break;
      } else if (digitalRead(button3Pin) == LOW) {
        Serial.println("Button pressed: 3");
        if (userInput.equalsIgnoreCase("fear")) {
          Serial.println("Matched: " + userInput);
          greenLedOn = true;
          playNote(330, 500);
        } else {
          Serial.println("Not matched");
          redLedOn = true;
          playNote(196, 500);
        }
        delay(1000);
        break;
      } else if (digitalRead(button4Pin) == LOW) {
        Serial.println("Button pressed: 4");
        if (userInput.equalsIgnoreCase("angry")) {
          Serial.println("Matched: " + userInput);
          greenLedOn = true;
          playNote(349, 500);
        } else {
          Serial.println("Not matched");
          redLedOn = true;
          playNote(220, 500);
        }
        delay(1000);
        break;
      } else if (digitalRead(button5Pin) == LOW) {
        Serial.println("Button pressed: 5");
        if (userInput.equalsIgnoreCase("neutral")) {
          Serial.println("Matched: " + userInput);
          greenLedOn = true;
          playNote(392, 500);
        } else {
          Serial.println("Not matched");
          redLedOn = true;
          playNote(262, 500);
        }
        delay(1000);
        break;
      }
    }

    digitalWrite(greenLedPin, greenLedOn ? HIGH : LOW);
    digitalWrite(redLedPin, redLedOn ? HIGH : LOW);
    userInput = "";
  }
}
```
## Block Diagram
![image](https://github.com/vmhashim/Autism-project/blob/main/ASD/Block%20Diagram.png)

## Results
The system effectively recognized and responded to RFID cards representing various emotions. It consistently provided accurate feedback, lighting up the green LED and producing corresponding auditory cues when users correctly matched emotions. The system also demonstrated robustness in handling user interactions, ensuring reliable performance throughout testing phases.

## Conclusion
In conclusion, the developed system showcases reliable functionality in facilitating emotion recognition through RFID technology and user interaction. It reliably provides feedback to enhance learning and engagement, demonstrating its potential as a valuable tool for aiding individuals with Autism Spectrum Disorder (ASD) in social and emotional development. Future iterations could explore additional features such as real-time data logging for progress tracking and integration with educational software to further enhance its educational impact and usability.

This structure highlights the system's performance and potential improvements, similar to the example you provided, but tailored to the context of your project on emotion recognition using RFID technology. Adjust the specifics based on your project's outcomes and future directions.
