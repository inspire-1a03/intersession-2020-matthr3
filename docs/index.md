# Ryan Matthews - 400120481
<!--
Welcome to your project page for Electronics for the Rest of Us. You'll use this page to describe and showcase your work throughout the module. 
A place for each deliverable has been created below for you in this markdown document. 
Note that comments (such as this) will not appear in the final markdown document (which you can view with the "Preview" button).
-->


## Day 1: Reflection
<!--
In this section, provide a ~250 word reflection on your first day of the module, and discuss why you're interested in this module and what you hope to take away from it.

-->
I had a great first day in my Electronics for the Rest of Us module, in which I got to learn how to utilize basic functions Arduino and GitHub through the exercises through the YouTube videos posted on the Inspire 1A03 webpage. I was interested in this module because I have not taken an electronics or computer science type course since grade 11. Although I found these courses quite fascinating in the underlining codes and functions that were necessary to run programs and other electronics, for whatever reason I never really continued to pursue this field. Leading to a degree in Communications and Political Science, I have yet to have the opportunity to take a computer or electronics type course since grade 11. “Electronics” is a broad word that has actively become a large part of the 21st century’s society. I was unsure of what we were going to endeavour in these modules when I selected it as my first choice for week three, but thought it would be a good idea to gain some insight into what it would have to offer. There’s no better way to practice an interdisciplinary learning style in learning how to exercise the tasks demonstrated in these modules. After day 1, I am looking forward to continuing the multifaceted platform of GitHub and learning how to create circuits using the Arduino.

<!-- 
You're also asked to insert a photo that represents your accomplishments on your first day. 
- Take a photo of you working or one of your circuits and upload it to the /docs/images/ folder of this repository. 
- Then, insert your photo into your document by modifying the markdown example that has been inserted below.
-->

<!--
Inserting an image takes the form: 
![image alt text](url/to/photo "Logo Title Text")
See the following webpage for more information: https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet#images
Replace the elements below to insert your picture.
--> 
![Ryan's Arduino Day 1](images/FIrstday.png "Ryan Matthews First Circuit!")

## Day 2: Results
<!--
Upload your fully-commented Arduino sketch from your final Day 2 build task--a thermometer connected to an RDB LED--into your GitHub repository.

--> 
```
/*
Adafruit Arduino - Lesson 3. RGB LED
*/

int redPin = 11;
int greenPin = 10;
int bluePin = 9;

//uncomment this line if using a Common Anode LED
//#define COMMON_ANODE

/*
 * Inputs ADC Value from Thermistor and outputs Temperature in Celsius
 *  requires: include <math.h>
 * Utilizes the Steinhart-Hart Thermistor Equation:
 *    Temperature in Kelvin = 1 / {A + B[ln(R)] + C[ln(R)]3}
 *    where A = 0.001129148, B = 0.000234125 and C = 8.76741E-08
 *
 * These coefficients seem to work fairly universally, which is a bit of a
 * surprise.
 *
 * Schematic:
 *   [Ground] -- [10k-pad-resistor] -- | -- [thermistor] --[Vcc (5 or 3.3v)]
 *                                               |
 *                                          Analog Pin 0
 *
 * In case it isn't obvious (as it wasn't to me until I thought about it), the analog ports
 * measure the voltage between 0v -> Vcc which for an Arduino is a nominal 5v, but for (say)
 * a JeeNode, is a nominal 3.3v.
 *
 * The resistance calculation uses the ratio of the two resistors, so the voltage
 * specified above is really only required for the debugging that is commented out below
 *
 * Resistance = PadResistor * (1024/ADC -1)  
 *
 * I have used this successfully with some CH Pipe Sensors (https://www.atcsemitec.co.uk/pdfdocs/ch.pdf)
 * which be obtained from https://www.rapidonline.co.uk.
 *
 */

#include <math.h>

#define ThermistorPIN 0                 // Analog Pin 0

float vcc = 4.91;                       // only used for display purposes, if used
                                        // set to the measured Vcc.
float pad = 9850;                       // balance/pad resistor value, set this to
                                        // the measured resistance of your pad resistor
float thermr = 10000;                   // thermistor nominal resistance

float Thermistor(int RawADC) {
  long Resistance;  
  float Temp;  // Dual-Purpose variable to save space.

  Resistance=pad*((1024.0 / RawADC) - 1);
  Temp = log(Resistance); // Saving the Log(resistance) so not to calculate  it 4 times later
  Temp = 1 / (0.001129148 + (0.000234125 * Temp) + (0.0000000876741 * Temp * Temp * Temp));
  Temp = Temp - 273.15;  // Convert Kelvin to Celsius                      

  // BEGIN- Remove these lines for the function not to display anything
  //Serial.print("ADC: ");
  //Serial.print(RawADC);
  //Serial.print("/1024");                           // Print out RAW ADC Number
  //Serial.print(", vcc: ");
  //Serial.print(vcc,2);
  //Serial.print(", pad: ");
  //Serial.print(pad/1000,3);
  //Serial.print(" Kohms, Volts: ");
  //Serial.print(((RawADC*vcc)/1024.0),3);  
  //Serial.print(", Resistance: ");
  //Serial.print(Resistance);
  //Serial.print(" ohms, ");
  // END- Remove these lines for the function not to display anything

  // Uncomment this line for the function to return Fahrenheit instead.
  //temp = (Temp * 9.0)/ 5.0 + 32.0;                  // Convert to Fahrenheit
  return Temp;                                      // Return the Temperature
}


void setup()
{
  pinMode(redPin, OUTPUT);
  pinMode(greenPin, OUTPUT);
  pinMode(bluePin, OUTPUT);  
  Serial.begin(115200);
}

void setColor(int red, int green, int blue)
{
  #ifdef COMMON_ANODE
    red = 255 - red;
    green = 255 - green;
    blue = 255 - blue;
  #endif
  analogWrite(redPin, red);
  analogWrite(greenPin, green);
  analogWrite(bluePin, blue);  
}
void loop()

 {
  float temp;
  temp = Thermistor(analogRead(ThermistorPIN));       // read ADC and  convert it to Celsius
  Serial.print("Celsius: ");
  Serial.print(temp,1);                             // display Celsius
  //temp = (temp * 9.0)/ 5.0 + 32.0;                  // converts to  Fahrenheit
  //Serial.print(", Fahrenheit: ");
  //Serial.print(temp,1);                             // display  Fahrenheit
  Serial.println("");                                  
  delay(5000);                                      // Delay a bit..


  if (temp > 25) {
  setColor(0, 255, 0);  // green
  }
  else if (temp > 10 and temp < 25) {
  setColor(0, 0, 255);  // blue
  }
  else if (temp < 10) {
  setColor(255, 255, 0);  // yellow
  }
  else{
  setColor(255, 0, 0);  // red
  }


}
```
<!--
Provide a short (~150 words) summary of your work on this circuit:
- How does your device work?
- What was challenging? 
- What worked? What didn't? 
- Be sure to link to your code (in your GitHub repository) in the text of your response.
 - The device

--> 
This device uses a thermestor to measure the variance in air temperature that is outputted on the computer screen through circuit created with the bread board and Arduino. The most challenging part of this deliverable was connecting the jumper cables to the right spot on the bread board to have the thermistor read the temperature properly. As I am still relatively new to using an Arduino setting it up properly to ensure the circuit was connected through the proper rows created some obstacles for me. The other part that was difficult in this deliverable was using if/else/elseif statements in my code. For a while it kept giving me errors in my setColour function but then I realized that that was not necessary so deleted it. It took a little extra time to get the code and jumper cables working in unison by I inevitably completed the task.

<!--

-->
## Arduino build-off results
<!--
Upload your fully-commented Arduino sketch from the final product of your Arduino build-off into the top-level of your module GitHub repository.
In ~300 words, provide a final device description and product pitch: 
- What does it do? Use a table (created in markdown) to list and describe the features. You can use the template provided below. 
- Describe briefly how it works.
- How could it be used in everyday life (or maybe just in rare cases)? 
- Be sure to link to your code (in your GitHub repository) in the text of your response.
- Include a snippet of code using the ``` ``` characters to display the code properly. 
Finally, record a short (30 second) video of a 'product pitch' for your device. 
- Upload the video to Youtube, and use the sample code below to embed your video.
-->
```
/*
Adafruit Arduino - Lesson 3. RGB LED
*/

int redPin = 11;
int greenPin = 10;
int bluePin = 9;

//uncomment this line if using a Common Anode LED
//#define COMMON_ANODE

/*
 * Inputs ADC Value from Thermistor and outputs Temperature in Celsius
 *  requires: include <math.h>
 * Utilizes the Steinhart-Hart Thermistor Equation:
 *    Temperature in Kelvin = 1 / {A + B[ln(R)] + C[ln(R)]3}
 *    where A = 0.001129148, B = 0.000234125 and C = 8.76741E-08
 *
 * These coefficients seem to work fairly universally, which is a bit of a
 * surprise.
 *
 * Schematic:
 *   [Ground] -- [10k-pad-resistor] -- | -- [thermistor] --[Vcc (5 or 3.3v)]
 *                                               |
 *                                          Analog Pin 0
 *
 * In case it isn't obvious (as it wasn't to me until I thought about it), the analog ports
 * measure the voltage between 0v -> Vcc which for an Arduino is a nominal 5v, but for (say)
 * a JeeNode, is a nominal 3.3v.
 *
 * The resistance calculation uses the ratio of the two resistors, so the voltage
 * specified above is really only required for the debugging that is commented out below
 *
 * Resistance = PadResistor * (1024/ADC -1)  
 *
 * I have used this successfully with some CH Pipe Sensors (https://www.atcsemitec.co.uk/pdfdocs/ch.pdf)
 * which be obtained from https://www.rapidonline.co.uk.
 *
 */

#include <math.h>

#define ThermistorPIN 0                 // Analog Pin 0

float vcc = 4.91;                       // only used for display purposes, if used
                                        // set to the measured Vcc.
float pad = 9850;                       // balance/pad resistor value, set this to
                                        // the measured resistance of your pad resistor
float thermr = 10000;                   // thermistor nominal resistance

float Thermistor(int RawADC) {
  long Resistance;  
  float Temp;  // Dual-Purpose variable to save space.

  Resistance=pad*((1024.0 / RawADC) - 1);
  Temp = log(Resistance); // Saving the Log(resistance) so not to calculate  it 4 times later
  Temp = 1 / (0.001129148 + (0.000234125 * Temp) + (0.0000000876741 * Temp * Temp * Temp));
  Temp = Temp - 273.15;  // Convert Kelvin to Celsius                      

  // BEGIN- Remove these lines for the function not to display anything
  //Serial.print("ADC: ");
  //Serial.print(RawADC);
  //Serial.print("/1024");                           // Print out RAW ADC Number
  //Serial.print(", vcc: ");
  //Serial.print(vcc,2);
  //Serial.print(", pad: ");
  //Serial.print(pad/1000,3);
  //Serial.print(" Kohms, Volts: ");
  //Serial.print(((RawADC*vcc)/1024.0),3);  
  //Serial.print(", Resistance: ");
  //Serial.print(Resistance);
  //Serial.print(" ohms, ");
  // END- Remove these lines for the function not to display anything

  // Uncomment this line for the function to return Fahrenheit instead.
  //temp = (Temp * 9.0)/ 5.0 + 32.0;                  // Convert to Fahrenheit
  return Temp;                                      // Return the Temperature
}

const int analogInPin = A0;  // Analog input pin that the potentiometer is attached to
const int analogOutPin = 9; // Analog output pin that the LED is attached to

int sensorValue = 0;        // value read from the pot
int outputValue = 0;        // value output to the PWM (analog out)

void setup()
{
  pinMode(redPin, OUTPUT);
  pinMode(greenPin, OUTPUT);
  pinMode(bluePin, OUTPUT);  
  Serial.begin(115200);
}

void setColor(int red, int green, int blue)
{
  #ifdef COMMON_ANODE
    red = 255 - red;
    green = 255 - green;
    blue = 255 - blue;
  #endif
  analogWrite(redPin, red);
  analogWrite(greenPin, green);
  analogWrite(bluePin, blue);  
}
void loop()

 {
  float temp;
  temp = Thermistor(analogRead(ThermistorPIN));       // read ADC and  convert it to Celsius
  Serial.print("Celsius: ");
  Serial.print(temp,1);                             // display Celsius
  //temp = (temp * 9.0)/ 5.0 + 32.0;                  // converts to  Fahrenheit
  //Serial.print(", Fahrenheit: ");
  //Serial.print(temp,1);                             // display  Fahrenheit
  Serial.println("");                                  
  delay(5000);                                      // Delay a bit..

    // read the analog in value:
  sensorValue = analogRead(analogInPin);
  // map it to the range of the analog out:
  outputValue = map;
  // change the analog out value:
  analogWrite(analogOutPin, outputValue);

  // print the results to the Serial Monitor:
  Serial.print("sensor = ");
  Serial.print(sensorValue);
  Serial.print("\t output = ");
  Serial.println(outputValue);

  // wait 2 milliseconds before the next loop for the analog-to-digital
  // converter to settle after the last reading:
  delay(2);


  if (temp > 25) {
  setColor(0, 255, 0);  // green
  }
  else if (temp > 10 and temp < 25) {
  setColor(0, 0, 255);  // blue
  }
  else if (temp < 10) {
  setColor(255, 255, 0);  // yellow
  }
  else{
  setColor(255, 0, 0);  // red
  }


}
```
<!--
Below is a general markdown table template. 
You can find more information at these links: 
- https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet#tables

-->
| Feature | Description | Other Notes |
|---------|-------------|-------------|
|- RGB LED Thermometer  |-  Using the thermistor that was utilized in our day two exercise,  implemented this into the assignment for day 3. Which is used to measure the variance in air temperature with the input of the thermistor and outputting the temperature onto the serial monitor in Arduino. Depending on the temperature, the RGB LED light will then correspond to the colour that it was told to within the code, this can be seen in " if (temp > 25) { setColor(0, 255, 0);  // green }". Thus, if the temperature is above 25 degrees celcius the RGB LED light will become green.|In everyday life this tool could be used to detect the temperature of the room. The limitations to it are that it must be connected to a computer to tell you the accurate temperature whereas a thermometer could do that much easier (but not as cool).             |
|- Photoresistor         | - As seen in exercise 15, the photoresistor detects how much light is being input into it. Sililar to the process of the thermistor, this will then output how much light there is onto the serial model in the Arduino code. This will range from 0 to 1023 and then be outputted back onto the LED light attached to it on the bread board, the higher the number the greater the brightness.            | - The practical use for this would once again be in a weather manner, to detect how sunny or cloudy it may be outside. When combined with the thermistor you will now know the temperature and the brightness.            |
| - Button        | - The button is attached to the circuit used for the photoresistor. The button must be pressed for the circuit to be closed allowing the current to flow through the circuit.             |             |
|         |             |             |

<!--
Below is an example of embedding a YouTube video in a markdown document for use in GitHub pages. 
Note that this video won't show when previewing the document in GitHub--it only works on the GitHub pages webpage. 
- Once your YouTube video is uploaded, right click and select ```<> Copy embed code```. 
- You can paste this code directly into your markdown document. 
- Note that you may want to adjust the width and height parameters to make it fit well in your webpage
-->

<iframe width="789" height="444" src="https://www.youtube.com/watch?v=ikoBO_KUjNM" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


## Final reflection & summary
<!--
In ~300 words:
- Summarize your experience in this module. What you learned, what you liked, what you found challenging.
- Reflect upon your learning and its relevance in your life.
-->

Throughout my experience I learned that I am not all that electronically advanced, but that's okay. Not everything I am going to pick up I am going to immediately excel at but it was the process of trying to learn something new that I enjoyed in taking this module in Inspire 1A03. Prior to this course I had never hear of an Arduino or gotten to experience any other hardware device that can be inputted into my computer. As mentioned on day 1, taking a computer science course in grade 11 was all software programming that would output onto the computer. Getting to build a weather station with the Arduino is unlike anything I had done before, and there were some learning experineces to go along with it. What I learned about myself in this module is that there are so many components to everyday utilities that I do not know about. It is important for me to continue to challenge myself and expand my knowledge across multiple sectors and fields of life to attempt to gain a baseline knowledge as to how the world we live in works. Throughout my academic experience, I have gained immense knowledge as to how the social and political world functions. I never really get the oppurtunity in my learning to focus on a hardware product such as this one to find the multiple utilities that a device may have. Stepping away from lecture hall, essay writing learning and into interdiscplinary learning helped me gain the hands on experience to complete this module.
