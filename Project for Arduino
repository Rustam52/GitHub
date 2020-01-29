#include <Wire.h>

#include <liquidCrystal_I2C.h> // подключаем библиотеку дисплея

 

liquidCrystal_I2C lcd(0x27, 16, 2);  // определяем дисплей

 

//uint8_t timer_reaciya;      // переменная куда пишется время замера реакции

int KolvoTruePush;            // количество верных нажатий

int KolvoFalsePush;           // количество не верных нажатий

int TekushyaKnoplaOgidaetca;  // в зависимости от режима, какая кнопка ожидается для нажатия (1 - первая кнопка, 2 - вторая кнопка) 

 

#define KolvoPopitok 6        // количество возможных попыток нажатия кнопок для любого из режимов

 

 

int Type_zamer = 0;           // режим замера  0 - простой; 1 - сложный;

boolean OnStartTimer = false; // начинаем замер времени

 

#define btnClear        3     // номер пина ардуины, кнопка сброс всех данных

#define btnType         4     // номер пина ардуины, кнопка тип сложности

#define btnStart        5     // номер пина ардуины, кнопка старт замера

#define btnReaciya1     6     // номер пина ардуины, кнопка реакция

#define btnReaciya2     7     // номер пина ардуины, кнопка реакция

 

#define ledStart        8     // номер пина ардуины, зеленый светодиод - начался замер реакции (простой и сложный режим)

#define ledStartHard    9     // номер пина ардуины, зеленый светодиод - начался замер реакции нельзя нажимать (только сложный режим)

#define ledFalse        10    // номер пина ардуины, желтый - ложное нажатие

#define ledOk           11    // номер пина ардуины, зеленый - нажал кнопку после начала замера

 

#define MyBizzed        12    // динамик (пищалка)

 

// таймеры подсчета времени реакции

volatile long msk100;

volatile long ms10;

volatile int cntr;

long tmillis, tms10=0;

uint8_t timer_reacе;  // примерное время рекции в милисекундах

//char flip;

 

void setup(){

 

  Serial.begin(9600);             // установка скорости обмена с компьютером по USART (USB)

  

  // настройка ножек ардуины

  pinMode(btnClear,   INPUT);     // установка пина на вход

  pinMode(btnReaciya, INPUT);     // установка пина на вход

  pinMode(btnType,    INPUT);     // установка пина на вход

  pinMode(btnStart,   INPUT);     // установка пина на вход

  

  

  

  // настройка вcтроенного таймера/счетчика ардуины, для подсчета времени реакции

  TCCR2A = 0;                     // Включаем нужный нам режим таймера/счетчика  - нормальный режим

  

  // Предделитель таймера/счетчика настраиваем на 16 

  // - это позволит "тикать" таймером каждую микросекунду

  // с учетом того что микроконтроллер работает с тактовой частотй 16 000 000 операций в секунду (16 Мгц)

  

  TCCR2B = 2;                     // 010 - fclk/8

  TCNT2  = 59;

  TIMSK2 |= (1 << TOIE2);         // разрешаем прерывание таймера/счетчика по переполнению (когда увеличение внутреннего числа, дойдет до предела, произойдет сброс на 0)

  

  

  // настройка дисплея

  lcd.init();                     // инициалзация дисплея

  

  lcd.clear();                    // очистим экран

  lcd.backlight();                // включаем подсветку

  

  lcd.setCursor(0,0);             // установка курсора в позицию буква = 0, строка = 0 

  lcd.print("Hello");             // Выводим на экран текст в первой строке

 

  PrintLCD_ToStart();             // выведим сообщение что нужно нажать кнопку старт

 

}

 

// настройка прерывания таймера/счетчика

ISR (TIMER2_OVF_vect){

 

  // взводим счетчик

  TCNT2  = 59;

  

  // прошли очередные 100 микросекунд - увеличиваем счетчик сотен микросекунд

  msk100++;

  

  cntr++;

  

  // прошли очередные 10 милисекнд - увеличиваем счетчик десятков милисекнд

  if (cntr > 99){

    ms10++;

    cntr = 0;

  }

  

}

 

void loop(){

 

  // была нажата кнопка старт и начался замер

  if (OnStartTimer == true){

    //timer_reaciya = timer_reaciya + 1; // считаем такты (нужно сделать замер и посмотреть сколь тактов в одной секунде)

    

    if (Type_zamer == 0){

      // простой режим (одна кнопка)

     //digitalWrite(MyBizzed, HIGH); // вкючаем светодиод? чтобы человек понял что замер начался

     digitalWrite(ledStart, HIGH); // вкючаем светодиод? чтобы человек понял что замер начался

     //digitalWrite(MyBizzed, LOW); // вкючаем светодиод? чтобы человек понял что замер начался

    }

    

    

    if (ms10 > tms10){

      tmillis = millis();

      tms10   = ms10;

      

      if (tms10%100 == 0){

        // if (flip){

        // }

        // else{

        // }

        

        // flip = !flip;

      }

      

      // if (tms10%1000 == 0) { // - это каждые 10 секунд

      if (tms10%10 == 0){       // - это каждые 1000 милисекунд

        timer_reacе = tmillis;

      }

    }

  } // if (OnStartTimer == true){

 

  // останавливаем таймер, при достижени количества попыток

  if (KolvoPopitok == (KolvoTruePush + KolvoFalsePush)){

    

                                         // выводим информацию о замере на экран

     lcd.clear();                        // очистим экран

     PrintLCD_Type(Type_zamer, 0);       // выводит тип режима замера в первой строке (простой или сложный)

     

     lcd.setCursor(0, 1);                // установка курсора в позицию буква = 0, строка = 2 

     lcd.print(timer_reacе);             // Выводим на экран результат замера во второй строке время в

     lcd.print(" Ms");                   // Выводим на экран результат замера во второй строке милисекунд

     lcd.print(" Er");                   // Выводим на экран результат замера во второй строке ошибки

     lcd.print(KolvoFalsePush);          // Выводим на экран результат замера во второй строке количество ошибок

     lcd.print("/");                     // Выводим на экран результат замера во второй строке

     lcd.print(KolvoPopitok);            // Выводим на экран результат замера во второй строке из KolvoPopitok

   

    OnStartTimer = false;                // остановили замер, пользователь нажал кнопку реакция

  

    digitalWrite(ledStart, LOW);         // выкючаем светодиод старт

    digitalWrite(ledStartHight , LOW);   // выкючаем светодиод старт сложный

    

    delay(30);                           // пауза 30 секунд

    

    lcd.clear();                        // очистим экран

    lcd.setCursor(0, 0);                // установка курсора в позицию буква = 0, строка = 2

    lcd.print("Push clear");

    

  } // конец if (KolvoPopitok == (KolvoTruePush + KolvoFalsePush))

  

  

  

  // кнопка реакция 1

   ButtonStateReaciya1 = digitalRead(btnReaciya1); 

   if (ButtonStatebtnReaciya1 == HIGH){

     

     if (Type_zamer == 0){                   // простой режим

        TekushyaKnoplaOgidaetca = 1;         // ожидаемая кнопка нажатия

     }

     else{

        TekushyaKnoplaOgidaetca = random(2); // ожидаемая кнопка нажатия случайный режим

       

     }

     

     

     if (TekushyaKnoplaOgidaetca == 1){      // простой режим

        KolvoTruePush = KolvoTruePush++;     // количество верных нажатий

       

       digitalWrite(ledStart, LOW);          // выкючаем светодиод старт

       delayMicroseconds(500);               // пауза 500 микросекунд, чтобы было видно мигание

       digitalWrite(ledStart, HIGH);         // выкючаем светодиод старт

     }

     else{

       digitalWrite(MyBizzed, HIGH); // вкючаем светодиод? чтобы человек понял что замер начался

       digitalWrite(ledFalse , HIGH);        // вкючаем светодиод ошибочного нажатия не той кнопки

        

       KolvoFalsePush = KolvoFalsePush++;    // количество не верных нажатий

       

       digitalWrite(ledStart, LOW);          // выкючаем светодиод старт

       digitalWrite(ledStartHight , LOW);    // выкючаем светодиод старт

       

       delayMicroseconds(500);               // пауза 500 микросекунд, чтобы было видно мигание

       

       digitalWrite(ledStart, HIGH);         // выкючаем светодиод старт

       digitalWrite(ledStartHight , HIGH);   // выкючаем светодиод старт

        

       digitalWrite(ledFalse , LOW);         // вкючаем светодиод ошибочного нажатия не той кнопки

       digitalWrite(MyBizzed, LOW); // вкючаем светодиод? чтобы человек понял что замер начался

     }

     

     lcd.clear();                        // очистим экран

     lcd.setCursor(0, 0);                // установка курсора в позицию буква = 0, строка = 2

     lcd.print("Push ");

     lcd.print(TekushyaKnoplaOgidaetca); // покажем на экране что нужно нажать

     

     

   } // конец if (ButtonStatebtnReaciya1 == HIGH){

   

   

   

  // кнопка реакция 2

  ButtonStateReaciya2 = digitalRead(btnReaciya2); 

  if (ButtonStatebtnReaciya2 == HIGH){

     

     if (Type_zamer == 1){                   // сложный режим

        TekushyaKnoplaOgidaetca = random(2); // ожидаемая кнопка нажатия случайный режим

     }

     else{

        TekushyaKnoplaOgidaetca = 1;         // ожидаемая кнопка нажатия

     }

     

     

     if (TekushyaKnoplaOgidaetca == 2){      // сложный режим

       KolvoTruePush = KolvoTruePush++;      // количество верных нажатий

        

       digitalWrite(ledStart, LOW);          // выкючаем светодиод старт

       digitalWrite(ledStartHight , LOW);    // выкючаем светодиод старт

       

       delayMicroseconds(500);               // пауза 500 микросекунд, чтобы было видно мигание

       

       digitalWrite(ledStart, HIGH);         // выкючаем светодиод старт

       digitalWrite(ledStartHight , HIGH);   // выкючаем светодиод старт

       

     }

     else{

        digitalWrite(MyBizzed, HIGH); // вкючаем светодиод? чтобы человек понял что замер начался

        digitalWrite(ledFalse , HIGH);       // вкючаем светодиод ошибочного нажатия не той кнопки

        

        KolvoFalsePush = KolvoFalsePush++;   // количество не верных нажатий

        

       digitalWrite(ledStart, LOW);          // выкючаем светодиод старт

       delayMicroseconds(500);               // пауза 500 микросекунд, чтобы было видно мигание

       digitalWrite(ledStart, HIGH);         // выкючаем светодиод старт

       digitalWrite(ledFalse , LOW);         // вкючаем светодиод ошибочного нажатия не той кнопки

       digitalWrite(MyBizzed, LOW); // вкючаем светодиод? чтобы человек понял что замер начался

 

     }

     

     lcd.clear();                        // очистим экран

     lcd.setCursor(0, 0);                // установка курсора в позицию буква = 0, строка = 2

     lcd.print("Push ");

     lcd.print(TekushyaKnoplaOgidaetca); // покажем на экране что нужно нажать

     

  } // конец if (ButtonStatebtnReaciya2 == HIGH){

   

   

   

   // кнопка старт замера

   ButtonStateStart = digitalRead(btnStart); 

   if (ButtonStateClear == HIGH){

    OnStartTimer = true; // запустили таймер замера

   }

   

   

   

   // кнопка тип

   ButtonStateType = digitalRead(Type); 

   if (ButtonStateType == HIGH){

    OnOffType();

   }

 

   

   

  // кнопка сброс

  ButtonStateClear = digitalRead(btnClear); // сделали сброс

  if (ButtonStateClear == HIGH){

    ClearAll();

    

    lcd.clear();                        // очистим экран

    lcd.setCursor(0, 0);                // установка курсора в позицию буква = 0, строка = 2

    lcd.print("Push start");

   }

   

 

    /*if (timer_reaciya == 100){

      // одна секунда

    elseif (timer_reaciya == 200){

      // две секунды

     elseif (timer_reaciya == 300){

      // три секунды

    }*/

   

   //lcd.setCursor(9, 1);             // move cursor to second line "1" and 9 spaces over

   //lcd.print(millis()/1000);       // display seconds elapsed since power-up

 

   //lcd.setCursor(0,1);             // move to the begining of the second line

   //lcd_key = read_LCD_buttons();   // read the buttons

 

   

} // loop()

 

// сбрасываем все замеры и таймеры

void ClearAll(){

 

  //timer_reaciya = 0; // сбрасываем все замеры

  

  // отключаем светодиоды

  digitalWrite(ledStart, LOW);        // вкючаем светодиод

  digitalWrite(ledStartHight , LOW);  // вкючаем светодиод

  digitalWrite(ledFalse , LOW);       // вкючаем светодиод

  digitalWrite(ledOk, LOW);           // вкючаем светодиод

  

  // сбрасываем таймеры

  Clear_Timer();

  

}

 

// сбрасываем таймер

void Clear_Timer(){

 

  // сбрасываем таймеры

  msk100 = 0; // счетчик сотен микросекунд, переполнение счетчика примерно  через 5 суток

  ms10   = 0; // счетчик десятков миллисекунд, переполнение счетчика примерно через 16 месяцев

  cntr   = 0;

  flip   = 0;

  

}

 

// Устанавливаем режим работы, простой или сложный

void OnOffType(){

 

  lcd.clear();                        // очистим экран

  

  if (Type_zamer == 0){

    Type_zamer = 1;                   // устанавливаем значение переменной Режима работы

    

    PrintLCD_Type(Type_zamer, 0);     // выводим нужный текст

    

  else

    Type_zamer = 0;                   // устанавливаем значение переменной Режима работы

    

    PrintLCD_Type(Type_zamer, 0);

    

  }

 

  PrintLCD_ToStart();                 // выводим сообщение что нужно нажать кнопку старт

  

}

 

 

void PrintLCD_ToStart(){

 

  lcd.setCursor(0,1);             // установка курсора в позицию буква = 0, строка = 1 

  lcd.print("Push Start");        // Выводим на экран текст во второй строке

 

}

 

// EnaleType - Тип режима: 

//  0 - Простой

//  1 - Сложный; 

//

//  LineDisplay - номер строки в которой выводим текст

//

void PrintLCD_Type(int EnaleType int LineDisplay){

 

  if (EnaleType == 0){

    lcd.setCursor(0, LineDisplay); // установка курсора в позицию буква = 0, строка = 0 

    lcd.print("T.Easy");           // Выводим на экран текст в первой строке Тип простой (сокращенно из-за того что у нас всего 8 символов)

  }

  else{

    lcd.setCursor(0, LineDisplay); // установка курсора в позицию буква = 0, строка = 0 

    lcd.print("T.Hard");           // Выводим на экран текст в первой строке Тип сложный

  }

  

}

 
