#include <Wire.h>                  //Шилд дисплея
#include <LiquidCrystal.h>         //Дисплей
#include <Servo.h> 

Servo motor;
LiquidCrystal lcd(8, 9, 4, 5, 6, 7 );

int button;
const int BUTTON_NONE   = 0;
const int BUTTON_RIGHT  = 1;
const int BUTTON_UP     = 2;
const int BUTTON_DOWN   = 3;
const int BUTTON_LEFT   = 4;
const int BUTTON_SELECT = 5;

int Speed = 1250;
int SSpeed = 1250;                 //Дублирующая переменная

int getPressedButton(){            //Подготовка клавиш
  int buttonValue = analogRead(0);
  if (buttonValue < 100) {
    return BUTTON_RIGHT;  
  }
  else if (buttonValue < 200) {
    return BUTTON_UP;
  }
  else if (buttonValue < 400){
    return BUTTON_DOWN;
  }
  else if (buttonValue < 600){
    return BUTTON_LEFT;
  }
  else if (buttonValue < 800){
    return BUTTON_SELECT;
  }
  return BUTTON_NONE;
}

void setup()
{
  Serial.begin(9600);
  motor.attach(16);
  lcd.begin(16, 2);  
  lcd.setCursor(0, 0); 
  lcd.print("Hi");    
  lcd.setCursor(0, 1); 
  lcd.print("Motor ready");          
}

void loop(){
  Key();
  ESC();
}

void Key(){                         //Аля кнопки
  button = getPressedButton();
  switch (button)
  {
  case BUTTON_RIGHT: 
    if (Speed <= 2250){
      Speed = Speed + 50;
    }
    break;
  case BUTTON_LEFT:
    if (Speed >= 1250){
      Speed = Speed - 50;
    }
    break;
  case BUTTON_UP:
    if (Speed < 2250){
      Speed = Speed + 100;
    }
    break;
  case BUTTON_DOWN:
    if (Speed >= 1300){
      Speed = Speed - 100;
    } 
    else if (Speed == 1250){
      Speed = Speed - 50;
    }
    break;
  case BUTTON_SELECT:
    Speed = 1200;
    break;
  }
  delay(200);                       //Ибо нажитие начинает дублироваться
  return true;
}

void ESC(){                         //Вывод оборотов на дисплей и передача данных на ESC
  if (SSpeed != Speed){
    if (Speed < 1250){
      lcd.setCursor(0, 1);
      lcd.print("             ");
      lcd.setCursor(0, 1); 
      lcd.print("Motor OFF");  
    }
    else{
      lcd.setCursor(0, 1);
      lcd.print("             ");
      lcd.setCursor(0, 1); 
      lcd.print("Motor ON");
    }
    motor.writeMicroseconds(Speed);
    lcd.setCursor(0, 0); 
    lcd.print("SPEED: "); 
    lcd.setCursor(7, 0); 
    lcd.print("       ");
    lcd.setCursor(7, 0);
    lcd.print(Speed);
    SSpeed = Speed;
  }
}
