# AE_tl_math
Copyright © Thomas Laforge. 2018
Free  use  of  tl_math  is
permitted under the guidelines and in accordance with the most current
version of the https://opensource.org/licenses/gpl-3.0.html.

The plugin uses Exprtk by Arash Partow
https://github.com/ArashPartow/exprtk


The plugin uses JSON for Modern C++
https://nlohmann.github.io/json/

An After Effect plugin project.
The user can write math expression for each pixels and by channels (RGBA). 
whatever your Color Space, the expected values are bewteen 0 and 1.


Verion note:

Alpha 04 (V1.12)

-new slider values range

-the preset has a name defined in thr script and now visible in the AE UI

-full json gateway from script to plugin

 Alpha 03 (v 1.11)
 
 -new script UI
 
 -new topics 
 
 -interaction with an other layer it's now possible (in topic input)
 
 
 Alpha 02 (v 1.1)
 
 -the plugin works with After Effect CC and above.
 
 -the plugin works in 8 and 16 bits (32bits coming soon) 
 
 -Possibilities to write an expression per channels
 
 -the plugin expressions can access to 4 sliders, two points and two colors pipets to comunicate with other AE elements.
 
 -presets can be saved as json files.
 
 -The expression language is based on Exprtk grammar  https://github.com/ArashPartow/exprtk
 
 
table of contents
01-HOW TO INSTALL
02-BASIC EXEMPLE
03-GRAMMAR RULES
04-VARIABLES
05-FUNCTIONS
 
 
**1-HOW TO INSTALL**

extract the zip file in the folder : 

On mac : /Library/Application Support/Adobe/Common/Plug-ins/7.0/MediaCore/tl

on Windows : C:\Program Files\Adobe\Common\Plug-ins\7.0\MediaCore\tl


**2- BASIC EXEMPLE**

-Apply the effect on a White Solid

-In the Effect UI click on "Math Expression"

-For the Alpha Channle write: xL/layerWidth

  The result is a ramp, black on the left white on the right. 
   for every pixel, the plugin devide the x coordonate by the total layerWidth of the layer.
   
   
**3- GRAMMAR RULES**

 3.1-The result
 Whatever the color Dpeth of your project (8/16 or 32 bits) with the plugin you have to think in float beween 0 and 1, like in 32 bits.  The plugin convert the result to the good depth of the project after the calculation.
 
 3.2-define a variable.
 Like in the After EFfect expression you can define a variable. It's compulsory to call var before to define a new one or you will get an error.
 
```
var foo := in_red*xL/layerWidth;
```
3.3-Sign Equal
The expression Math is based on Exprtk grammar. That's why equal is written like this 
```
:=
```
Note : the plugin will correct your if you write " = " (with spaces before and after). But when you will reopen the expression it will be changed in  " := "

3.4 close your "phrase"
```
;
```
As in javascript, don't forget to close the end of your phrase with  ";"

3.4 operators
  Math Operators are similar to operators used in the the Internal Expression system of After Effect.
  
  
NAME| DESCRIPTION|EXEMPLE|
---------|--------|--------|
`+`      |  addition| var foo:= a+b;|
`-`      | substraction|  var foo:= a-b;|
`*`  | multiply|var foo:= a*b;  |
`/`  | devide|var foo:= a/b;    |

 Boolean Operators
 Logical operators have differents name than in internal expression system but they do the same things
 
 
 NAME| DESCRIPTION|EXEMPLE|
---------|--------|--------|
`and` '&'       |  logical and. Returns true if a and b are true | a and b; a&b;|
`or`  | logical or. Returns true if a or b is true  |  a or b; |
`nand`  | logical NAND Returns true if a and b are false | a nand b;|
`nor`| logical NOR Returns true if a or b is false | a nor b;|

3.5 LOOPS AND CONDITIONS


3.1 IF/ELSE

```
if (a < b)
  {1}
else if (a==b) 
 {0.5}
else
  {0}  
```

3.2 LOOPS 

Loops are similar to those like in js. you can use loops for, while, switch.  Be careful, if you write a big loop, the effect will calculate it for each pixels, o limit the range of the loop or take a coffee during render time.

exemple with a loop, a condition and a break: 
```
var a :=0;
for (far i:=0; i<10; i +=1){
   if (layerHeight/yL >i)
      {
          a:=i;
          break;
      }
}
```

Note the plugin converts "&&" and "||" to the exprtk grammar.



**4-THE VARIABLES**

4.1 Pixel Access

NAME| TYPE | DESCRIPTION|
---------|--------|--------|
`xL`       | float  | Coordonate of the current pixel on the x axis|
`yL`       | float  | Coordonate of the current pixel on the y axis|
`in_red`  | float  | value of red channel from the current pixel|
`in_green`| float  | value of green channel from the current pixel|
`in_blue` | float  |  value of blue channel from the current pixel|
`in_alpha`| float  |  value of alpha channel from the current pixel|
`in_luma` | float  | value of luma  from the current pixel|
`extL_red`| float  | value of red channel from the input layer with time and pixel offset|
`extL_green`| float  | value of green channel from the input layer with time and pixel offset|
`extL_blue`| float  | value of blue channel from the input layer with time and pixel offset|
`extL_alpha`| float  | value of alphachannel from the input layer with time and pixel offset|

4.2 Matrix 3*3of pixels around

You can access to the pixels around with the Vec3_channel[mat] function where mat is an integer between 0 and 8.
In the layout, the number 4 represents the current pixel, the other number represents the index of each pixel arounds  
   
 0 | 1 | 2 |
---|---|---|
 3 | **4 CURRENT PIXEL** | 5 |
 6 | 7 | 8 |

MATRIX NAME| TYPE INPUT | TYPE OUPUT| DESCRIPTION|
---------|--------|--------|--------|
`vec3_red[mat]`  | int   | float  | Access to the red channel from pixel around. mat is an integer between 0 and 8
`vec3_green[mat]`  | int   | float  | Access to the green channel from pixel around. mat is an integer between 0 and 8
`vec3_blue[mat]`  | int   | float  | Access to the blue channel from pixel around. mat is an integer between 0 and 8
`vec3_alpha[mat]`  | int   | float  | Access to the alpha channel from pixel around. mat is an integer between 0 and 8

4.3 Effect UI parameters

The plugin has some classical parameters to communicate with the other After Effects parameters, other effects and the classical expressions. The next table describe the access to the value with the math Parser.

NAME| TYPE | DESCRIPTION|
---------|--------|--------|
`var1`| float | Value of the slider variable 1 |
`var2`| float | Value of the slider variable 2 |
`var3`| float | Value of the slider variable 3 |
`var4`| float | Value of the slider variable 4|
`pt1_x`| float | X value of Point one |
`pt1_y`| float | Y value of Point one |
`pt2_x`| float | X value of Point Two |
`pt2_y`| float  | Y value of Point Two  |
`cl1_red`| float | red value of parameter Color One |
`cl1_green`| float  | green value of parameter Color One |
`cl1_blue`| float  | blue value of parameter Color One |
`cl2_red`| float  | red value of parameter Color Two |
`cl2_green`| float | green value of parameter Color Two |
`cl2_blue`| float | blue value of parameter Color Two |

4.4 Layer Properties

Access to the transform properties of the current layer from the math expressions

NAME| TYPE | DESCRIPTION|
---------|--------|--------|
`layerWidth` | float | Width of the layer |
`layerHeight` | float | height of the layer |
`layerTime_sec` | float | Current time of the layer in seconds |
`layerTime_frame` | float | Current time of the layer in frames |
`layerDuration` | float | Duration of the layer |
`layerPosition_x` | float | Position of the layer on the x axis | 
`layerPosition_y` | float | Position of the layer on the y axis |
`layerPosition_z` | float | Position of the layer on the z axis |
`layerScale_z` | float | Scale of the layer  on the x axis | 
`layerScale_y` | float | Scale of the layer  on the y axis | 
`layerScale_z` | float | Scale of the layer  on the z axis | 

4.5 Composition properties

Access to the compositions properties

NAME| TYPE | DESCRIPTION|
---------|--------|--------|
`compWidth`| float |  Width of the composition |
`compHeight` | float | Height of the composition |
`compFps` | float |   Return the parameter number of frame per seconds of the composition |


6- FUNCTIONS

non exaustive list of avaibles functions
 abs, acos, acosh, asin, asinh, atan, atanh, ceil, cos,  cosh,
       cot, csc,  deg2grad, deg2rad,  erf, erfc,  exp, expm1, floor,
       frac, grad2deg, log, log10, log1p, log2, rad2deg, round, sec,
       sgn, sin, sinc, sinh, sqrt, swap, tan, tanh, trunc
