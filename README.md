# EnterCode-Arduino
EnterCode. Is a Thing What you enter a code. if the password is good then opens but thats a text and the same thing if you have the password wrong. And Opensource To Edit And Make Things do no text mabye a sound or more
#

# Instructions

## PART 0: Required Components

Before starting, make sure you have the following parts:

| Quantity | Component                                 |
| -------- | ----------------------------------------- |
| 1        | Arduino Uno (or compatible Arduino board) |
| 1        | 16×2 I2C LCD Screen                       |
| 1        | 4×4 Matrix Hex Keypad                     |
| 1        | Breadboard                                |
| 16       | Male-to-Male Jumper Wires (cables)        |
| 1        | USB Cable for Arduino                     |

### Component Notes

* The **16×2 I2C LCD Screen** is used to display prompts, entered digits, and access messages.
* The **4×4 Matrix Hex Keypad** is used to enter the password.
* The **breadboard** distributes power and makes wiring easier.
* The **16 jumper wires** are used to connect the Arduino, LCD, keypad, and breadboard together.
* The **USB cable** is required for uploading the code and powering the Arduino.

## PART 1: Prepare Your Computer (Software)

Before touching the hardware, make sure your computer is ready to talk to the Arduino:

1. Open the Arduino IDE on your computer.
2. Go to the top menu:

   * **Sketch → Include Library → Manage Libraries...**
3. In the search box, find and install these two specific libraries:

   * **Keypad** (by Mark Stanley)
   * **LiquidCrystal I2C** (by Frank de Brabander)

---

## PART 2: Step-by-Step Wiring Instructions

> ⚠️ **Important:** Unplug your Arduino from the computer while wiring so nothing short-circuits.

We will use the side rails of the white breadboard to distribute power.

### Step 1: Power the Breadboard

1. Connect a jumper wire from the **5V** pin on your Arduino to the **Red Line (+)** on the side of the breadboard.
2. Connect a jumper wire from a **GND** pin on your Arduino to the **Blue Line (-)** on the side of the breadboard.

Now:

* Any component plugged into the **Red Line (+)** gets **5V power**.
* The **Blue Line (-)** acts as **ground (GND)**.

---

### Step 2: Connect the LCD Screen (Green Rectangle)

Look at the 4 pins on the back of the screen adapter and connect them as follows:

| LCD Pin | Connect To                  |
| ------- | --------------------------- |
| GND     | Blue Line (-) on breadboard |
| VCC     | Red Line (+) on breadboard  |
| SDA     | Analog Pin A4 on Arduino    |
| SCL     | Analog Pin A5 on Arduino    |

---

### Step 3: Connect the Keypad (Green Button Board)

The keypad has a row of 8 pins at the top. Connect them from left to right:

| Keypad Pin        | Arduino Pin   |
| ----------------- | ------------- |
| Pin 1 (far left)  | Digital Pin 2 |
| Pin 2             | Digital Pin 3 |
| Pin 3             | Digital Pin 4 |
| Pin 4             | Digital Pin 5 |
| Pin 5             | Digital Pin 6 |
| Pin 6             | Digital Pin 7 |
| Pin 7             | Digital Pin 8 |
| Pin 8 (far right) | Digital Pin 9 |

---

## PART 3: The Complete Program Code

Copy this code into the Arduino IDE and click the **Upload** button.

```cpp
#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <Keypad.h>

// Inicjalizacja LCD (Adres 0x27, 16 kolumn, 2 wiersze)
LiquidCrystal_I2C lcd(0x27, 16, 2);

// TUTAJ WPISZ SWOJE HASŁO
const String CORRECT_PASSWORD = "1234";
String enteredPassword = "";

// Konfiguracja układu klawiatury 4x4
const byte ROWS = 4;
const byte COLS = 4;
char hexKeypadMap[ROWS][COLS] = {
  {'1','5','9','#'},
  {'2','6','0','R'},
  {'3','7','0','*'},
  {'4','8','9','*'}
};

// Mapowanie pinów klawiatury do Arduino (Piny 2 do 9)
byte rowPins[ROWS] = {2, 3, 4, 5};
byte colPins[COLS] = {6, 7, 8, 9};

Keypad customKeypad = Keypad(makeKeymap(hexKeypadMap), rowPins, colPins, ROWS, COLS);

void setup() {
  lcd.init();
  lcd.backlight();
  showStartScreen();
}

void loop() {
  // Odczyt wciśniętego klawisza
  char customKey = customKeypad.getKey();
  
  if (customKey) {
    if (customKey == '#') { 
      // Klawisz '#' działa jako ENTER (zatwierdzenie hasła)
      checkPassword();
    }
    else if (customKey == '*') { 
      // Klawisz '*' resetuje wpisane znaki
      enteredPassword = "";
      showStartScreen();
    }
    else if (customKey == 'R') { 
      // Klawisz 'R' resetuje wpis bez pokazywania ekranu ładowania (brak zawieszeń)
      enteredPassword = "";
      showStartScreen();
    }
    else if (customKey != 'B' && customKey != 'C' && customKey != 'D') {
      // Jeśli wciśnięto cyfrę, dodaj ją do wpisanego hasła
      if (enteredPassword.length() < CORRECT_PASSWORD.length()) {
        enteredPassword += customKey;
        // Wyświetla dokładnie ten znak, który został wciśnięty (hasło jest widoczne)
        lcd.print(customKey);
      }
    }
  }
}

void checkPassword() {
  lcd.clear();
  if (enteredPassword == CORRECT_PASSWORD) {
    lcd.print("ACCESS GRANTED");
    lcd.setCursor(0, 1);
    lcd.print("Opening...");
    delay(2000);
    lcd.clear();
    // Here You Type You message here is the 1st line
    lcd.print("You can Edit");
    lcd.setCursor(0, 1);
    // And Here Is The 2nd Line 
    lcd.print("This in the code");
    delay(5000); 
  } 
  else {
    lcd.print("WRONG PASSWORD!");
    delay(2000);
  }
  
  // Czyszczenie danych i powrót do ekranu startowego
  enteredPassword = "";
  showStartScreen();
}

void showStartScreen() {
  lcd.clear();
  lcd.print("Enter code:");
  lcd.setCursor(0, 1); // Ustawienie kursora w drugim wierszu dla widocznych cyfr
}



```

---

## PART 4: Operating Instructions

Once the code has uploaded successfully:

1. Look at the LCD screen.

2. The screen will display:

   ```
   Enter code:
   ```

3. Enter your code using the keypad buttons.

   * The default password is **1234**.
   * The exact numbers you type will appear on the second row of the display.

4. Press the **#** button to submit the password.

   * If correct, the display will show:

   ```
   ACCESS GRANTED
   Welcome!
   ```

5. Press the ***** button at any time to clear the entered code and start over.

---

## Customizing the Password

To change the password, edit this line:

```cpp
const String CORRECT_PASSWORD = "1234";
```

Replace `"1234"` with your desired numeric code, for example:

```cpp
const String CORRECT_PASSWORD = "5959";
```

## Customizing the Text

To change the text, edit this line:

```cpp
    // Here You Type You message here is the 1st line
    lcd.print("You can Edit");
    lcd.setCursor(0, 1);
    // And Here Is The 2nd Line 
    lcd.print("This in the code");
    delay(5000); 
```

Replace `"1234"` with your desired numeric code, for example:

```cpp
    // Here You Type You message here is the 1st line
    lcd.print("And I Replace");
    lcd.setCursor(0, 1);
    // And Here Is The 2nd Line 
    lcd.print("The Text!");
    delay(5000); 
```

