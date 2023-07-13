# TCL_WORKSHOP_VSD
# Introduction
TCL Tool command language is widely known as robust scripting language with many programming elements. It is easily accessed by Windows, MAC OS and Unix operating systems users. TCL is used for scritping programs, desktop applications(GUI), graphical user interfaces, networking, testing and web.
TCL scripting introduces basic ideas like variables and command substitution, globbing and Regular Expressions, files, and process handling. The process of creating apps that rely on libraries and packages is demonstrated. 
For Electronics Design Automation applications i.e., EDA, TCL has emerged as the industry standard embedded command language. When a automated repetitive behavior, expansion of application capabilities, developing a GUI, managing many tools with a single script are needed, TCL is the ideal option. 
# Objective 
When an input in the form of excel sheet with information about details of the files, constraints is provided, we use TCL scripting in converting the excel format into tool(YOSYS and OpenTimer) undserstandable formats thus generating pre-layout timing reports in the form of the data sheet.
# Table of contents
* [DAY 1 Intro to TCL and SINDHUSYNTH Toolbox Usage](#DAY-1-Intro-to-TCL-and-SINDHUSYNTH-Toolbox-Usage)
* [DAY 2 Variable Creation and Processing Constraints from CSV](#DAY-2-Variable-Creation-and-Processing-Constraints-from-CSV)
* [DAY 3 Processing Clock and Input Constraints](#DAY-3-Processing-Clock-and-Input-Constraints)
* [DAY 4 YOSYS Synthesis Intro](#DAY-4-YOSYS-Synthesis-Intro)
* [DAY 5 ADVANCED SCRIPTING and QUALITY OF RESULTS GENERATION](#DAY-5-ADVANCED-SCRIPTING-and-QUALITY-OF-RESULTS-GENERATION)
  
# DAY 1 Intro to TCL and SINDHUSYNTH Toolbox Usage
## TASK, SUBTASK and SCENARIOS
## Creating a TASK
Task is it to build a user interface **(TCL BOX)** that will take an excel sheet as an input and provide output as datasheet.
```
input - openMSP430_design_details.csv (excel sheet)
libreoffice openMSP430_design_details.csv
```
![inputs to tcl box](https://github.com/sindhuk95/unknown/assets/135046169/0edc6361-b2fb-46fc-9cdf-499a1e1e5113)

![inputs to tcl box](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/1271f039-b63c-4ce6-86cf-1151040366a6)

In this course we implement TCL script which is in the bottom line of the shell script **(sindhusynth)**. The below image is content of TCL Box.

![sindhusynth](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/8d4d26ea-9cb8-4ebf-bd8f-0facca42f53a)

## SUBTASK
For top down approach, lets divide task into few more sub task for easy implementation.
1. create command and pass the .csv from the unix shell to TCL script.
2. Convert all inputs to format [1] and SDC format and feed it to synthesis YOSYS tool.
3. Convert format [1] and SDC to format [2] and feed it to OpenTimer timing tool.
4. Generate output report

### 1. Create a command and pass the .csv from the unix shell to TCL script.
The command was created with the following algorithm
- We provide a **shebang** for the operating system to excute the file in a specified script.
```
#!/bin/tcsh -f
```
- Executable Permissions
```
chmod -R 777 sindhusynth
```
- Now lets create a logo using the algorithm
```
#!/bin/tcsh -f 
echo
echo
echo "     *****     ******     ********  ******** ***        **** ********  *******  ********    "
echo "    **   **    **    **      **        **    ** **     ** **    **    *********    **       "
echo "   **     **   **      **    **        **    **  **   **  **    **    **           **       "
echo "  **       **  **      **    **        **    **   ** **   **    **    ***          **       "
echo " **         ** **     **     **        **    **    ***    **    **    ****         **       "
echo " **         ** **   **       **        **    **           **    **    *********    **       "
echo " **         ** ** **         **        **    **           **    **         ****    **        "
echo "  **       **  **            **        **    **           **    **          ***    **       "
echo "   **     **   **            **        **    **           **    **           **    **       "
echo "    **   **    **            **        **    **           **    **    *********    **       "
echo "     *****     **            **     ******** **           ** ********  *******     **       "
echo
echo
echo "             A pessimist always sees difficulty in every opportunity               "
echo
echo "             An optimist always sees opportunity in every difficulty                "
echo
echo "      Now lets dive into the course and learn how we can generate timing reports    "
echo
echo "                    after synthesis using YOSYS and Open Timer  "
echo
echo "                Thank You kunal gosh for this course and the quote "
echo
```
Execution looks like

![synth log](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/4abc0e6d-1c26-4936-846c-b4c993dd09a8)

Since we are passing the input as argument, Lets consider few scenarios for User point of view
We script few lines of code for these scnearios.
### Scenario 1 When no argument is passed i.e., no input is provided by the user
script written is 
```
if($#argv != 1)then
       echo "Info: Please provide the csv file, Thank you"
       exit 1
endif
```
Here argv is argument that need to passed next to the script file. From the below command, it is seen that no argument has been passed.

The command prompt shows the above script info saying

![scn1](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/550225a6-64f0-49a6-b7cf-b9cc5dbf492d)

### Scenario 2 - Where user passes an argument that is not present in our design.

Code for this in the file is

```
f (! -f $argv[1] || $argv[1] == "-help") then
            if ($argv[1] != "-help") then
                 echo "Error: cannot find csv file $argv[1]. Exiting..."
                 exit 1
```
When i gave an incorrect argument like below, the above code gets executed and displays the error on the command prompt

![scen 2](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/df51a77e-a249-40df-8b88-14d8ff6b1974)

### Scenario 3 - where user passes argument as help
code
```
 if (! -f $argv[1] || $argv[1] == "-help") then
            if ($argv[1] != "-help") then
                 echo "Error: cannot find csv file $argv[1]. Exiting..."
                 exit 1
            else
                 echo USAGE : ./sindhusynth \<csv file\>
                 echo         where \<csv file\> consists of 2 columns, below keyword being in 1st column and is case sensistive. Please reques VSD team for sample csv file
                 echo
                 echo         \<Design Name\> is the name of top level module
                 echo
                 echo         \<Output Directory\> is the name of the output directory where you want to dump synthesis script, synthesised netlist and timing reports
                 echo
                 echo         \<Netlist Directory\> is the name of the directory where RTL netlists present
                 echo
                 echo         \<Early Library Path\> is the file path of the early cell library to be used for STA
                 echo
                 echo         \<Late Library Path\> is the file path of the late cell library to be used for STA
                 echo
                 echo         \<Constarints\> is csv file path of constraints to be used for STA
                 echo
                 exit 1
           end
```
When help is passed as an argument, the command prompt displays the above code

![scn3](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/272ae04e-ce4a-4ec0-a16a-0f8f666d02a0)

### Pass the csv file to tcl script from the unix shell

``` 
tclsh sindhusynth.tcl $argv[1]
```
Here argv[1] is our input csv file.

# DAY 2 Variable Creation and Processing Constraints from CSV
### 2. Convert all inputs to format [1] and SDC format and feed it to synthesis YOSYS tool.
- Create Variables
- Check if Direcotories and files mentioned in .csv exists or not
- Read "Constraints File" for above .csv and convert to sdc format
- Read all files in "Netlist DIrectory"
- Create main synthesis script in format[2]
- pass this script to YOSYS

### Create Variables

steps in TCL script for creating variables.
1. read the .csv file contents into matrix m  i.e., read2matrix
2. Link the matrix to array **my_arr(col,row)**
3. Next string mapping for extra spaces
   
```
if {![regexp {^csv} $input] || $argc!=1} {
        puts "Error in usage"
        puts "Usage: .sindhusynth <.csv>"
        puts "where <.csv> file has below inputs"
} else {
#-------------------------------------------------------------------------------------------------------------------------------------------#
#-----convert .csv to matrix and create initial varialbes "DesignName OutputDirectory NetlistDIrectory EarlyLibraryPath LateLibraryPath"----#
#----------If you are modifiying this script, Plase us above variables as starting point. Use "puts" command to report above variables------#
#-------------------------------------------------------------------------------------------------------------------------------------------#

        set filename [lindex $argv 0]
        package require csv
        package require struct::matrix
        struct::matrix m
        set f [open $filename]
        csv::read2matrix $f m , auto
        close $f
        set columns [m columns]
        #m add coulmns $columns
        m link my_arr
        set num_of_rows [m rows]
        set i 0

        while {$i < $num_of_rows} {
                puts "\n Info: setting $my_arr(0,$i) as '$my_arr(1,$i)'"
                if {$i == 0} {
                        set [string map {" " ""} $my_arr(0,$i)] $my_arr(1,$i)
                } else {
                        set [string map {" " ""} $my_arr(0,$i)] [file normalize $my_arr(1,$i)]
                }
                set i [expr {$i+1}]

        }
}

puts "\n Info: Below are the list of initial variables and their values. User can use these variables for further debug. Use 'puts <variable name>' command to query value of beloew variables"
puts "DesignName = $DesignName"
puts "OutputDirectory = $OutputDirectory"
puts "NetlistDirectory = $NetlistDirectory"
puts "EarlyLibraryPath = $EarlyLibraryPath"
puts "LateLibraryPath = $LateLibraryPath"
```
Once the above piece of code is executed, we get the file names displayed on the command prompt

![auto creation of variables](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/f7ec98e0-a652-491e-9955-24e7636d7d41)

### Check if Direcotories and files mentioned in .csv exists or not

```
if {! [file isdirectory $OutputDirectory] } {
        puts "\nInfo : cannot find output Directory $OutputDirectory. Creating $OutputDirectory"
        file mkdir $OutputDirectory
} else {
        puts "\nInfo: Output directory found in path $OutputDirectory"
} if {! [file isdirectory $NetlistDirectory] } {
        puts "\nError: cannot find RTL netlist directory in path $NetlistDirectory. Exiting..."
        exit
} else {
        puts "\nInfo: RTL netlist directory found in path $NetlistDirectory"
} if {! [file exists $EarlyLibraryPath] } {
        puts "\n Error: cannot find the early cell library in path $EarlyLibraryPath. Exiting..."
        exit
} else {
        puts "\nInfo: Early cell library found in path $EarlyLibraryPath"
} if {! [file exists $LateLibraryPath] } {
        puts "\n Error: cannot find the early cell library in path $LateLibraryPath. Exiting..."
        exit
} else {
        puts "\nInfo: Early cell library found in path $LateLibraryPath"
} if {! [file exists $ConstraintsFile] } {
        puts "\n Error: cannot find the constraints file in path $ConstraintsFile. Exiting..."
        exit
} else {
        puts "\nInfo: constraints file found in path $ConstraintsFile"
```
Now, prompt displays the above code when correct files were provided

![files and driectories exits](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/7f51ba63-9aab-41a9-b9f3-a2098b2f4cb8)

when user gives a wrong path for files, an error will be displayed

![error for missing fdiles or wrong paths](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/645f2ef4-8e54-4518-a419-df79bcdd8ed7)


Few Steps before reading the constraint file is

1. Dump the constraints
2. read the file to matrix called m, read2matrix
3. link the matrix to the array **my_arr(colu,rows)**
4. string mapping

![dumping constraints](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/9c9e49bd-2db3-4663-8695-ef3cf7f090d8)

Inorder to access the constraint file, we need to know the columns and rows for clocks,inputs and outputs and process them

![cio rows and columns](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/2abd1683-cb9e-4ff1-95dd-23764b38fe55)

![rows and columns](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/12fbde27-081f-4c47-8e8c-e15017904796)

# DAY 3 Processing Clock and Input Constraints

## Clocks Processing
### Clock Latency and Clock transition constraints
Using the number of rows and columns of the clock, we get all the parameteres rekated to clock. From the above image, we can see few variables for rows and columns count.
number of rows is given by variable ``` $clock_start``` and  number of columns is give by variable ``` $clock_start_column``` 
```
#------------------clock constraints-------------------#
#---------------clock latency constraints--------------#
#-------------------algorithm--------------------------#
set clock_early_rise_delay_start [lindex [lindex [constraints search rect $clock_start_column $clock_start [expr {$no_of_columns-1}] [expr {$input_ports_start-1}] early_rise_delay] 0 ] 0]
set clock_early_fall_delay_start [lindex [lindex [constraints search rect $clock_start_column $clock_start [expr {$no_of_columns-1}] [expr {$input_ports_start-1}] early_fall_delay] 0 ] 0]
set clock_late_rise_delay_start [lindex [lindex [constraints search rect $clock_start_column $clock_start [expr {$no_of_columns-1}] [expr {$input_ports_start-1}] late_rise_delay] 0 ] 0]
set clock_late_fall_delay_start [lindex [lindex [constraints search rect $clock_start_column $clock_start [expr {$no_of_columns-1}] [expr {$input_ports_start-1}] late_fall_delay] 0 ] 0]
#------------------------clock transition constraints----------------------------------#
set clock_early_rise_slew_start [lindex [lindex [constraints search rect $clock_start_column $clock_start [expr {$no_of_columns-1}] [expr {$input_ports_start-1}] early_rise_slew] 0 ] 0]
set clock_early_fall_slew_start [lindex [lindex [constraints search rect $clock_start_column $clock_start [expr {$no_of_columns-1}] [expr {$input_ports_start-1}] early_fall_slew] 0 ] 0]
set clock_late_rise_slew_start [lindex [lindex [constraints search rect $clock_start_column $clock_start [expr {$no_of_columns-1}] [expr {$input_ports_start-1}] late_rise_slew] 0 ] 0]
set clock_late_fall_slew_start [lindex [lindex [constraints search rect $clock_start_column $clock_start [expr {$no_of_columns-1}] [expr {$input_ports_start-1}] late_fall_slew] 0 ] 0]
```
![clock constraints](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/8999acf3-30bd-4602-a638-b9546ca2e1b7)

Using the above variables, let us create the clock parameters

```
set sdc_file [open $OutputDirectory/$DesignName.sdc "w"]
set i [expr {$clock_start+1}]
set end_of_ports [expr {$input_ports_start-1}]
puts "\nInfo-SDC : Working on clock constraints..."
while { $i < $end_of_ports } {
        puts -nonewline $sdc_file "\ncreate_clock -name [constraints get cell 0 $i] -period [constraints get cell 1 $i] -waveform \{0 [expr {[constraints get cell 1 $i] * [constraints get cell 2 $i]/100}]\} \[get_ports [constraints get cell 0 $i]\]"
        puts -nonewline $sdc_file "\nset_clock_latency -source -early -rise [constraints get cell $clock_early_rise_delay_start $i] \[get_clock [constraints get cell 0 $i]\]"
        puts -nonewline $sdc_file "\nset_clock_latency -source -early -fall [constraints get cell $clock_early_fall_delay_start $i] \[get_clock [constraints get cell 0 $i]\]"
        puts -nonewline $sdc_file "\nset_clock_latency -source -late -rise [constraints get cell $clock_late_rise_delay_start $i] \[get_clock [constraints get cell 0 $i]\]"
        puts -nonewline $sdc_file "\nset_clock_latency -source -late -fall [constraints get cell $clock_late_fall_delay_start $i] \[get_clock [constraints get cell 0 $i]\]"
        puts -nonewline $sdc_file "\nset_clock_transition -rise -min [constraints get cell $clock_early_rise_slew_start $i] \[get_clock [constraints get cell 0 $i]\]"
        puts -nonewline $sdc_file "\nset_clock_transition -fall -min [constraints get cell $clock_early_fall_slew_start $i] \[get_clock [constraints get cell 0 $i]\]"
        puts -nonewline $sdc_file "\nset_clock_transition -rise -max [constraints get cell $clock_late_rise_slew_start $i] \[get_clock [constraints get cell 0 $i]\]"
        puts -nonewline $sdc_file "\nset_clock_transition -fall -max [constraints get cell $clock_late_fall_slew_start $i] \[get_clock [constraints get cell 0 $i]\]"
        set i [expr {$i+1}]
}
```
Once we create these parameters, these are dumped into sdc file 

![clock constraints 2](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/094bf0ed-5c7a-4e57-83a3-08fe65666971)


## Inputs Processing

Clock ports are standard ports but the ports under inputports are not standard ports as some are single bit and some are multi bit. Multi bit are called buses.SO

Steps for input ports processing
* Set variables for all the input parameters
```
set input_early_rise_delay_start [lindex [lindex [constraints search rect $clock_start_column $input_ports_start [expr {$no_of_columns-1}] [expr {$output_ports_start-1}] early_rise_delay] 0 ] 0]
set input_early_fall_delay_start [lindex [lindex [constraints search rect $clock_start_column $input_ports_start [expr {$no_of_columns-1}] [expr {$output_ports_start-1}] early_fall_delay] 0 ] 0]
set clock_late_rise_slew_start [lindex [lindex [constraints search rect $clock_start_column $input_ports_start [expr {$no_of_columns-1}] [expr {$output_ports_start-1}] late_rise_slew] 0 ] 0]
set clock_late_fall_slew_start [lindex [lindex [constraints search rect $clock_start_column $input_ports_start [expr {$no_of_columns-1}] [expr {$output_ports_start-1}] late_fall_slew] 0 ] 0]
set related_clock [lindex [lindex [constraints search rect $clock_start_column $input_ports_start [expr {$no_of_columns-1}] [expr {$output_ports_start-1}] clocks] 0 ]0]
```
* Indicate if its a bus by appending a '*' in front of the port. we can do this by
  - Get input port i.e., grep for a particular port/pattern ex: cpu_en, dbg_i2c_addr
  - open all the netlist files(.v) in a serial format by ```set netlist [glob -dir $NetlistDirectory *.v]```
  - open a temporary file under write mode. ```set tmp_file [open /tmp/1 w]```
  - now traverse/grep for input ports through all the files and each line in the file until End of all files(EOF)
  - Since we get multiple declarations of the patterns in inputs and outputs, we can split each string using ';' as a delimiter and use lindex[0] to get the first declaration
  - Grep for string "input" in patterns generated by above step
  - If there are multiple spaces,remove them and replace with single space as it makes a unique pattern and makes it easy to filter
  - Now, Count the number of elements in the string and save it as $count.
  - If $count < 2 - its a single bit wire else its a multibit bus and put '*' infronnt of the port
  - Similar to clock ports ,send the input ports data to SDC file

```
set netlist [glob -dir $NetlistDirectory *.v]
        set tmp_file [open /tmp/1 w]
        foreach f $netlist {
                set fd [open $f]
                while {[gets $fd line] != -1} {
                        set pattern1 " [constraints get cell 0 $i];"
                        if {[regexp -all -- $pattern1 $line]} {
                                set pattern2 [lindex [split $line ";"] 0]
                                if {[regexp -all {input} [lindex [split $pattern2 "\S+"] 0]]} {
                                        set s1 "[lindex [split $pattern2 "\S+"] 0] [lindex [split $pattern2 "\S+"] 1] [lindex [split $pattern2 "\S+"] 2]"
                                        puts -nonewline $tmp_file "\n[regsub -all {\s+} $s1 " "]"
                                }
                        }
                }
                close $fd
        }
close $tmp_file

set tmp_file [open /tmp/1 r]
set tmp2_file [open /tmp/2 w]

puts -nonewline $tmp2_file "[join [lsort -unique [split [read $tmp_file] \n]] \n]"

close $tmp_file
close $tmp2_file

set tmp2_file [open /tmp/2 r]
set count [llength [read $tmp2_file]]


if {$count > 2} {
        set inp_ports [concat [constraints get cell 0 $i]*]
} else {
        set inp_ports [constraints get cell 0 $i]
}
```
![output input process](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/7c6c2370-2a36-4ec8-a139-73fe30307adb)


**Input Ports created in sdc file**

![sdc file inputs](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/c6245c79-a929-40b3-af85-d6b8324b1ece)

Grep the input port names just for observation

![input port name](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/87308b0d-9e7a-4cb7-895d-4a48dbcb2584)

In the same manner, output ports are declared and created in sdc,
we set variables for output parameters

```
set output_early_rise_delay_start [lindex [lindex [constraints search rect $clock_start_column $output_ports_start [expr {$no_of_columns-1}] [expr {$no_of_rows-1}] early_rise_delay] 0 ] 0]
set output_early_fall_delay_start [lindex [lindex [constraints search rect $clock_start_column $output_ports_start [expr {$no_of_columns-1}] [expr {$no_of_rows-1}] early_fall_delay] 0 ] 0]
set output_late_rise_delay_start [lindex [lindex [constraints search rect $clock_start_column $output_ports_start [expr {$no_of_columns-1}] [expr {$no_of_rows-1}] late_rise_delay] 0 ] 0]
set output_late_fall_delay_start [lindex [lindex [constraints search rect $clock_start_column $output_ports_start [expr {$no_of_columns-1}] [expr {$no_of_rows-1}] late_fall_delay] 0 ] 0]
set output_load_start [lindex [lindex [constraints search rect $clock_start_column $output_ports_start [expr {$no_of_columns-1}] [expr {$no_of_rows-1}] load] 0 ] 0]
set related_clock [lindex [lindex [constraints search rect $clock_start_column $output_ports_start [expr {$no_of_columns-1}] [expr {$no_of_rows-1}] clocks] 0 ] 0]
```

we search for output till end of the files in all the netlist files in our design and repeat grep, split, delimiter steps.
```
set netlist [glob -dir $NetlistDirectory *.v]
        set tmp_file [open /tmp/1 w]
        foreach f $netlist {
                set fd [open $f]
                while {[gets $fd line] != -1} {
                        set pattern1 " [constraints get cell 0 $i];"
                        if {[regexp -all -- $pattern1 $line]} {
                                set pattern2 [lindex [split $line ";"] 0]
                                if {[regexp -all {output} [lindex [split $pattern2 "\S+"] 0]]} {
                                        set s1 "[lindex [split $pattern2 "\S+"] 0] [lindex [split $pattern2 "\S+"] 1] [lindex [split $pattern2 "\S+"] 2]"
                                        puts -nonewline $tmp_file "\n[regsub -all {\s+} $s1 " "]"
                                }
                        }
                }
                close $fd
        }
close $tmp_file

set tmp_file [open /tmp/1 r]
set tmp2_file [open /tmp/2 w]

puts -nonewline $tmp2_file "[join [lsort -unique [split [read $tmp_file] \n]] \n]"

close $tmp_file
close $tmp2_file

set tmp2_file [open /tmp/2 r]
set count [llength [read $tmp2_file]]


if {$count > 2} {
        set op_ports [concat [constraints get cell 0 $i]*]
} else {
        set op_ports [constraints get cell 0 $i]
}
```
![output output process](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/adb496b0-8579-4617-8854-bfe808c3d946)

Output port names are created in sdc files

![sdc out files](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/81bf1213-d5fe-443d-9e41-c2434f42c2d6)

Grep the output port names for observation

![output portname](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/c3042764-fbdd-4228-9e7b-89fbc8abaaa9)

# DAY 4 YOSYS Synthesis Intro
## YOSYS (YOSYS Open Synthesis Suite)
Yosys is a framework for Verilog RTL synthesis. Yosys is controlled using synthesis scripts which reads a design from the verilog file, synthesizes it to a gate-level netlist using the cell library and writes the synthesized results as Verilog netlist. YOSYS supports technology mapping, optimization, and formal verification. It integrates with other EDA tools, and is widely used in academic and industry for digital design tasks.

**Hierarchy Check** : create hierarchy check script to be used by yosys. 
```
puts "\nInfo: Creating hierarchy check script to be used by Yosys"
set data "read_liberty -lib -ignore_miss_dir -setattr blackbox ${LateLibraryPath}"
puts "data is \"$data\""
set filename "$DesignName.hier.ys"
puts "filename is \"$filename\""
set fileId [open $OutputDirectory/$filename "w"]
puts "open \"$OutputDirectory/$filename\" in write mode"
puts -nonewline $fileId $data

set netlist [glob -dir $NetlistDirectory *.v]
puts "netlist is \"$netlist\""
foreach f $netlist {
	set data $f
	puts "data is \"$f\""
	puts -nonewline $fileId "\nread_verilog $f"
}
puts -nonewline $fileId "\nhierarchy -check"
close $fileId

puts "\nclose \"$OutputDirectory/$filename\"\n"
puts "\nhierarchy checking------"
```
![yosys hierarchy](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/2d4c7ccf-c511-4585-a3fa-c5d5c2c15121)


Check whether all the modules are coded correctly

```
if { [catch { exec yosys -s $OutputDirectory/$DesignName.hier.ys >& $OutputDirectory/$DesignName.hierarchy_check.log} msg]} {
	set filename "$OutputDirectory/$DesignName.hierarchy_check.log"
	set pattern {referenced in module}
	set count 0
	set fid [open $filename r]
	while {[gets $fid line] != -1} {
		incr count [regexp -all -- $pattern $line]
		if {[regexp -all -- $pattern $line]} {
			puts "\nError: module [lindex $line 2] is not part of design $DesignName. Please correct RTL in the path '$NetlistDirectory'" 
			puts "\nInfo: Hierarchy check FAIL"
		}
	}
	close $fid
} else {
	puts "\nInfo: Hierarchy check PASS"
}
puts "\nInfo: Please find hierarchy check details in [file normalize $OutputDirectory/$DesignName.hierarchy_check.log] for more info"
cd $working_dir
```

![hierarchy err_flag 0](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/a0fce480-a6bd-4d9f-b856-393faf000935)


**Error Handling** : reference module error due to incorrectly coded or module doesnt exist 

![hierarchy err_flag 1](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/bc4e460a-7444-433a-af8b-d301681e94f4)

**Error in the error log file**

![error in log file](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/e5b2921a-abdb-4e29-b155-938e496b7d6d)


# DAY 5 ADVANCED SCRIPTING and QUALITY OF RESULTS GENERATION
## Synthesis main file scritping and run
```
puts "\nInfo: Creating main synthesis script to be used by Yosys"
set data "read_liberty -lib -ignore_miss_dir -setattr blackbox ${LateLibraryPath}"
set filename "$DesignName.ys"
set fileId [open $OutputDirectory/$filename "w"]
puts -nonewline $fileId $data

set netlist [glob -dir $NetlistDirectory *.v]
foreach f $netlist {
        set data $f
        puts -nonewline $fileId "\nread_verilog $f"
}
puts -nonewline $fileId "\nhierarchy -top $DesignName"
puts -nonewline $fileId "\nsynth -top $DesignName"
puts -nonewline $fileId "\nsplitnets -ports -format ___\nproc; memory; opt; fsm; opt\ntechmap; opt\ndfflibmap -liberty ${LateLibraryPath}"
puts -nonewline $fileId "\nabc -liberty ${LateLibraryPath} "
puts -nonewline $fileId "\nflatten"
puts -nonewline $fileId "\nclean -purge\niopadmap -outpad BUFX2 A:Y -bits\nopt\nclean"
puts -nonewline $fileId "\nwrite_verilog $OutputDirectory/$DesignName.synth.v"
close $fileId
puts "\nInfo: Synthesis script created and can be accessed from path $OutputDirectory/$DesignName.ys"

puts "\nInfo: Running synthesis........"

if {[catch { exec yosys -s $OutputDirectory/$DesignName.ys >& $OutputDirectory/$DesignName.synthesis.log} msg]} {
	puts "\nError: Synthesis failed due to errors. Please refer to log $OutputDirectory/$DesignName.synthesis.log for errors"
	exit
} else {
	puts "\nInfo: Synthesis finished successfully"
}
puts "\nInfo: Please refer to log $OutputDirectory/$DesignName.synthesis.log"
```

![synthesis success](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/36d45fcc-602f-4cb1-b00a-08a17f21b387)

 
## Synthesised Netlist

![final synth file](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/103baa01-9f06-47ed-9f14-0636ddde5375)

# Need and script to edit Yosys Output Netlist
## Converting format[1] to format [2]
Through format[1] we got sunthesised netlist, sdc. To feed these inputs to opentimer tool, formats of theses two files should be changed. we have to remove out some redundant symbols and lines So that other tool can understand this RAW file

From the above synthesised image, we can see few \`s and *`s at the beinging of the file. 
* Grep the * and remove it
* string mapping backslashes with nospace

### Remove the *s and blackslahes

```
set fileId [open /tmp/1 "w"]
puts -nonewline $fileId [exec grep -v -w "*" $OutputDirectory/$DesignName.synth.v]
close $fileId

set output [open $OutputDirectory/$DesignName.final.synth.v "w"]

set filename "/tmp/1"
set fid [open $filename r]
        while {[gets $fid line] != -1} {
	puts -nonewline $output [string map {"\\" ""} $line]
	puts -nonewline $output "\n"
}
close $fid
close $output

puts "\nInfo: Please find the synthesized netlist for $DesignName at below path. You can use this netlist for STA or PNR"
puts "\n$OutputDirectory/$DesignName.final.synth.v"

```
### Original synthesized file with *s and \s present

![new start file with count](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/2076b1ac-b13e-4b64-a734-26e3a6c8ee02)

### Netlist after removing *s and \s

![final synth file](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/6c36b235-20f7-4087-a2f4-e8ce590b0367)

Synthesised netlist in opentimer understandable format is produced. In the same way we have to do for all the reset the input files need for opentimer like sdc,lib.
For this we used **PROCS**

#PROCS
Proc is just like a function. We can define it in a file and then just source that proc to our main script and call that proc just like any other function by passing the required options and arguments. Here is an example of one of the proc that is used to define the number of threads.

Here I will be creating a proc to source other procs in my main tcl file and its name is proc_help
```
proc proc_help {args} {
        if {$args != "-prochelp"} {
                puts "Invalid Command.... use : -prochelp"
        } else {

                source /home/vsduser/vsdsynth/procs/read_lib.proc
                source /home/vsduser/vsdsynth/procs/read_sdc.proc
                source /home/vsduser/vsdsynth/procs/read_verilog.proc
                source /home/vsduser/vsdsynth/procs/reopenStdout.proc
                source /home/vsduser/vsdsynth/procs/set_num_threads.proc
        }
}
```
From the above code, we have 5 procs sourced in main tcl file. Lets see what each one does

* **reopenStdout.proc**
  
This stdout proc is using to close the screen log and dump into another file

```
reopenStdout.proc
proc reopenStdout {file} {
    close stdout
    open $file w
}
```
* **set_num_threads.proc**
```
proc set_multi_cpu_usage {args} {
        array set options {-localCpu <num_of_threads> -help "" }
        #foreach {switch value} [array get options] {
        #puts "Option $switch is $value"
        #}
        while {[llength $args]} {
        #puts "llength is [llength $args]"
        #puts "lindex 0 of \"$args\" is [lindex $args 0]"
                switch -glob -- [lindex $args 0] {
                -localCpu {
                           #puts "old args is $args"
                           set args [lassign $args - options(-localCpu)]
                           #puts "new args is \"$args\""
                           puts "set_num_threads $options(-localCpu)"
                          }
                -help {
                           #puts "old args is $args"
                           set args [lassign $args - options(-help) ]
                           #puts "new args is \"$args\""
                           puts "Usage: set_multi_cpu_usage -localCpu <num_of_threads>"
                      }
                }
        }
}
```
```
puts "\nInfo: Timing Analysis Started...."
puts "\nInfo: Initializing number of threads, libraries, sdc, verilog netlist path..."
source /home/vsduser/vsdsynth/procs/reopenStdout.proc
source /home/vsduser/vsdsynth/procs/set_num_threads.proc
reopenStdout $OutputDirectory/$DesignName.conf
set_multi_cpu_usage -localCpu 6
```
![first_config_file](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/d349007d-2d62-474f-81ca-b29b33a80da8)

- "array set options { -localCpu <num_of_threads> -help "" }" --> set an array named options. options is a list of key-value pairs, where each key is a string representing the element's name, and each value is the corresponding value to assign to that element. eg, "-localCpu is linked to <num_of_threads>" and "-help" is linked to "".
- "switch -glob -- [lindex $args 0]" --> globbing is used to get the term inside [] so that switch can map to the corresponding case. Takes only the ket of the key-value pair
- "set args [lassign $args - options(-localCpu)]" --> assigning new value to args after removing the array element which was used to enter the loop

* **read_lib.proc**


```
proc read_lib args {
	array set options {-late <late_lib_path> -early <early_lib_path> -help ""}
	while {[llength $args]} {
		switch -glob -- [lindex $args 0] {
		-late {
			set args [lassign $args - options(-late) ]
			puts "set_late_cell_lib_fpath $options(-late)"
		      }
		-early {
			set args [lassign $args - options(-early) ]
			puts "set_early_cell_lib_fpath $options(-early)"
		       }
		-help {
			set args [lassign $args - options(-help) ]
			puts "Usage: read_lib -late <late_lib_path> -early <early_lib_path>"
			puts "-late <provide late library path>"
			puts "-early <provide early library path>"
		      }	
		default break
		}
	}
}
```
![lib conf](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/92966065-048c-4fb6-95b0-9d78602a3bb9)

Similar to the set_num_threads proc , the read_lib proc will have 3 options i.e late early and help
the proc ensures to read the late and early lib file for STA and write it in a file

* **read-verilog.proc**
```
read_verilog.proc
proc read_verilog arg1 {
  puts "set_verilog_fpath $arg1"
}
```
This proc enters the puts statement followed by the netlist file

![verilog file](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/752a5c69-4b40-445c-9842-3c6c78204194)

* **read_sdc.proc**

For this particular proc ,we will make subdivisions of it and look into the functioning
In the SDC file that we generated previously, it had square brackets and additional symbols which the Opentimer tool doesnt recognize. So we use this proc to arrive at a final file format that Opentimer can Use from SDC File

* **Remove the square brackets from sdc file**
```
proc read_sdc {arg1} {
set sdc_dirname [file dirname $arg1]
set sdc_filename [lindex [split [file tail $arg1] .] 0 ]
set sdc [open $arg1 r]
set tmp_file [open /tmp/1 w]

puts -nonewline $tmp_file [string map {"\[" "" "\]" " "} [read $sdc]]
close $tmp_file
```
![sdc name](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/141d849c-3f1f-45f0-9de5-c41c7ac0e4bf)

sdc files after removing brackets

![sdc file with removed square brackets](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/882e55d5-42aa-44fe-a55c-cfdf223435d6)

* **Split the lines based on newline charachter and create clock constraints**
  
```
set tmp_file [open /tmp/1 r]
set timing_file [open /tmp/3 w]
set lines [split [read $tmp_file] "\n"]
set find_clocks [lsearch -all -inline $lines "create_clock*"]
foreach elem $find_clocks {
        set clock_port_name [lindex $elem [expr {[lsearch $elem "get_ports"]+1}]]
        set clock_period [lindex $elem [expr {[lsearch $elem "-period"]+1}]]
        set duty_cycle [expr {100 - [expr {[lindex [lindex $elem [expr {[lsearch $elem "-waveform"]+1}]] 1]*100/$clock_period}]}]
        puts $timing_file "clock $clock_port_name $clock_period $duty_cycle"
        }
close $tmp_file
```
![clock constraints](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/74c16bea-efd8-41ee-8556-042b239dd690)

tmp file tmp1 has intermediate data i.e., sdc file with removed brackets and tmp3 has final timing file which in this stage have clock constraints required for opentimer

![sdc file with removed square brackets and clock format](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/5921df88-a5a6-47ad-934d-2e07c231a126)

* **Convert clock latency constraints**
  
```
#-----------------------------------------------------------------------------#
#----------------converting set_clock_latency constraints---------------------#
#-----------------------------------------------------------------------------#

set find_keyword [lsearch -all -inline $lines "set_clock_latency*"]
set tmp2_file [open /tmp/2 w]
set new_port_name ""
foreach elem $find_keyword {
        set port_name [lindex $elem [expr {[lsearch $elem "get_clocks"]+1}]]
        if {![string match $new_port_name $port_name]} {
                set new_port_name $port_name
                set delays_list [lsearch -all -inline $find_keyword [join [list "*" " " $port_name " " "*"] ""]]
                set delay_value ""
                foreach new_elem $delays_list {
                        set port_index [lsearch $new_elem "get_clocks"]
                        lappend delay_value [lindex $new_elem [expr {$port_index-1}]]
                }
                puts -nonewline $tmp2_file "\nat $port_name $delay_value"
        }
}

close $tmp2_file
set tmp2_file [open /tmp/2 r]
puts -nonewline $timing_file [read $tmp2_file]
close $tmp2_file
```
![clock latency](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/9d7a985b-02c5-483d-874c-3730426fa92e)

tmp file tmp2 has intermediate data i.e., delay values and tmp3 has final timing file which in this stage have clock and its latency constraints required for opentimer

![tmp files after clock latency](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/7f2532e3-35a0-45f4-8485-2974dd40da1e)

* **Convert clock transition constraints**
```
set find_keyword [lsearch -all -inline $lines "set_clock_transition*"]
set tmp2_file [open /tmp/2 w]
set new_port_name ""
foreach elem $find_keyword {
        set port_name [lindex $elem [expr {[lsearch $elem "get_clocks"]+1}]]
        if {![string match $new_port_name $port_name]} {
                set new_port_name $port_name
                set delays_list [lsearch -all -inline $find_keyword [join [list "*" " " $port_name " " "*"] ""]]
                set delay_value ""
                foreach new_elem $delays_list {
                        set port_index [lsearch $new_elem "get_clocks"]
                        lappend delay_value [lindex $new_elem [expr {$port_index-1}]]
                }
                puts -nonewline $tmp2_file "\nslew $port_name $delay_value"
        }
}

close $tmp2_file
set tmp2_file [open /tmp/2 r]
puts -nonewline $timing_file [read $tmp2_file]
close $tmp2_file
```
![clock transition](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/6362a1b8-fbe0-42a7-9eca-aa315e469e91)

tmp file tmp2 has intermediate data i.e., clock transition i.e., slew values and tmp3 has final timing file which in this stage have clock constraints required for opentimer

![tmp files after transition](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/50639b81-b48a-4685-9e9e-09b051a2d85d)

similarly we do for inputs and outputs , you can refer to this proc under resources section

* **Convert input delay constraints**

```
set find_keyword [lsearch -all -inline $lines "set_input_delay*"]
set tmp2_file [open /tmp/2 w]
set new_port_name ""
foreach elem $find_keyword {
        set port_name [lindex $elem [expr {[lsearch $elem "get_ports"]+1}]]
        if {![string match $new_port_name $port_name]} {
                set new_port_name $port_name
        	set delays_list [lsearch -all -inline $find_keyword [join [list "*" " " $port_name " " "*"] ""]]
		set delay_value ""
        	foreach new_elem $delays_list {
        		set port_index [lsearch $new_elem "get_ports"]
        		lappend delay_value [lindex $new_elem [expr {$port_index-1}]]
        	}
        	puts -nonewline $tmp2_file "\nat $port_name $delay_value"
	}
}
close $tmp2_file
set tmp2_file [open /tmp/2 r]
puts -nonewline $timing_file [read $tmp2_file]
close $tmp2_file
```
![input delay constra](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/630c4830-75fa-4766-99c8-84fe834acace)

tmp file tmp2 has intermediate data i.e., input delays and tmp3 has final timing file which in this stage have clock constraints, input delays required for opentimer

![tmp files after input](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/7c00876d-f40a-4e5d-994d-636cfb0c619b)

* **Convert input transition constraints**
```
set find_keyword [lsearch -all -inline $lines "set_input_transition*"]
set tmp2_file [open /tmp/2 w]
set new_port_name ""
foreach elem $find_keyword {
        set port_name [lindex $elem [expr {[lsearch $elem "get_ports"]+1}]]
        if {![string match $new_port_name $port_name]} {
                set new_port_name $port_name
        	set delays_list [lsearch -all -inline $find_keyword [join [list "*" " " $port_name " " "*"] ""]]
        	set delay_value ""
        	foreach new_elem $delays_list {
        		set port_index [lsearch $new_elem "get_ports"]
        		lappend delay_value [lindex $new_elem [expr {$port_index-1}]]
        	}
        	puts -nonewline $tmp2_file "\nslew $port_name $delay_value"
	}
}

close $tmp2_file
set tmp2_file [open /tmp/2 r]
puts -nonewline $timing_file [read $tmp2_file]
close $tmp2_file
```

tmp file tmp2 has intermediate data i.e., input transition i.e., slew values and tmp3 has final timing file which in this stage have clock and input constraints required for opentimer

![tmp files after total input](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/4bafc09a-3052-4dbc-b8a5-13a035ffaa81)

* **Convert output delay constraints**
```
set find_keyword [lsearch -all -inline $lines "set_output_delay*"]
set tmp2_file [open /tmp/2 w]
set new_port_name ""
foreach elem $find_keyword {
        set port_name [lindex $elem [expr {[lsearch $elem "get_ports"]+1}]]
        if {![string match $new_port_name $port_name]} {
                set new_port_name $port_name
        	set delays_list [lsearch -all -inline $find_keyword [join [list "*" " " $port_name " " "*"] ""]]
        	set delay_value ""
        	foreach new_elem $delays_list {
        		set port_index [lsearch $new_elem "get_ports"]
        		lappend delay_value [lindex $new_elem [expr {$port_index-1}]]
        	}
        	puts -nonewline $tmp2_file "\nrat $port_name $delay_value"
	}
}

close $tmp2_file
set tmp2_file [open /tmp/2 r]
puts -nonewline $timing_file [read $tmp2_file]
close $tmp2_file
```

tmp file tmp2 has intermediate data i.e., output delays and tmp3 has final timing file which in this stage have clock and input constraints, output delays required for opentimer

![tmp files after output delays](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/39697934-e9fd-4c3a-8406-5cb13ddc85bd)


* **Convert output load constraint**
```
set find_keyword [lsearch -all -inline $lines "set_load*"]
set tmp2_file [open /tmp/2 w]
set new_port_name ""
foreach elem $find_keyword {
        set port_name [lindex $elem [expr {[lsearch $elem "get_ports"]+1}]]
        if {![string match $new_port_name $port_name]} {
                set new_port_name $port_name
        	set delays_list [lsearch -all -inline $find_keyword [join [list "*" " " $port_name " " "*" ] ""]]
        	set delay_value ""
        	foreach new_elem $delays_list {
        	set port_index [lsearch $new_elem "get_ports"]
        	lappend delay_value [lindex $new_elem [expr {$port_index-1}]]
        	}
        	puts -nonewline $timing_file "\nload $port_name $delay_value"
	}
}
close $tmp2_file
set tmp2_file [open /tmp/2 r]
puts -nonewline $timing_file  [read $tmp2_file]
close $tmp2_file
close $timing_file
```
![output load](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/bb63a8ed-e788-44d2-9220-2b077254ff55)

tmp file (tmp3) has final timing file which in this stage have all the clock, input and output constraints required for opentimer

![tmp files after output](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/51b78d4a-f9a2-48e3-9195-6a5c0f8725e2)

check for bussed input and output ports in tmp3 file

![stars in tmp3 file](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/ebd25474-acff-4ddf-90cc-16c62a26e803)

Convert these bussed ports into bit blasted ports by using netlist. Check the bit blasted ports in netlist using grep.

![grep input and output](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/a4e7bde7-6c66-401b-a7d2-f6c966ae04a7)

```
set ot_timing_file [open $sdc_dirname/$sdc_filename.timing w]
set timing_file [open /tmp/3 r]
while {[gets $timing_file line] != -1} {
        if {[regexp -all -- {\*} $line]} {
                set bussed [lindex [lindex [split $line "*"] 0] 1]
                set final_synth_netlist [open $sdc_dirname/$sdc_filename.final.synth.v r]
                while {[gets $final_synth_netlist line2] != -1 } {
                        if {[regexp -all -- $bussed $line2] && [regexp -all -- {input} $line2] && ![string match "" $line]} {
                        puts -nonewline $ot_timing_file "\n[lindex [lindex [split $line "*"] 0 ] 0 ] [lindex [lindex [split $line2 ";"] 0 ] 1 ] [lindex [split $line "*"] 1 ]"
                        } elseif {[regexp -all -- $bussed $line2] && [regexp -all -- {output} $line2] && ![string match "" $line]} {
                        puts -nonewline $ot_timing_file "\n[[lindex [lindex [split $line "*"] 0 ] 0 ] [lindex [lindex [split $line2 ";"] 0 ] 1 ] [lindex [split $line "*"] 1 ]"
                        }
                }
        } else {
        puts -nonewline $ot_timing_file  "\n$line"
        }
}

close $timing_file
puts "set_timing_fpath $sdc_dirname/$sdc_filename.timing"
}
```
![bussed to bit ports](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/f5769dac-a8bb-45de-b05b-ef10941f76e1)

lets check what tmp3 file has

![tmp file with bussed ports](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/692c91c5-fb7f-4642-a2c8-3f2aa6317197)

These bussed ports are converted to bit blased ports in the timimg file

![bussed to bit blasted ports in timimg file](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/d4f14de9-8818-4fce-bb62-1971b4c3fd3a)

* **Creating a .spef and .conf file**
**Master file** provided to the OpenTimer tool to perform STA. This config file has paths for netlist, timing, library, spef. It also consists of timer and reports generation for worst slack, worst paths.
Before proceeding any further, lets create spef file.
Both conf and spef file are more of puts statements 
```
if {$enable_prelayout_timing == 1} {
        puts "\nInfo : enable_prelayout_timing is $enable_prelayout_timing.Enabling zero-wire load parasitics.........."
        set spef_file [open $OutputDirectory/$DesignName.spef w]
        puts $spef_file "*SPEF \"IEEE 1481-1998\""
        puts $spef_file "*DESIGN \"$DesignName\""
        puts $spef_file "*DATE \"Mon Jul 10 06:36:15 2023\""
        puts $spef_file "*VENDOR \"VLSI System Design\""
        puts $spef_file "*PROGRAM \"VSD TCL Workshop\""
        puts $spef_file "*VERSION \"0.0\""
        puts $spef_file "*DESIGN FLOW \"NETLIST_TYPE_VERILOG\""
        puts $spef_file "*DIVIDER /"
        puts $spef_file "*DELIMITER : "
        puts $spef_file "*BUS_DELIMITER [ ]"
        puts $spef_file "*T_UNIT 1 PS"
        puts $spef_file "*C_UNIT 1 FF"
        puts $spef_file "*R_UNIT 1 KOHM"
        puts $spef_file "*L_UNIT 1 UH"
}
close $spef_file
set conf_file [open $OutputDirectory/$DesignName.conf a]
# a in above line end indicates append mode
puts $conf_file "set_spef_fpath $OutputDirectory/$DesignName.spef"
puts $conf_file "init_timer"
puts $conf_file "report_timer"
puts $conf_file "report_wns"
puts $conf_file "report_tns"
puts $conf_file "report_worst_paths -numPaths 10000 "
close $conf_file
```
**Spef file**

![spef](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/3b016044-70fd-4147-856a-24f05ba9a2ed)

**Conf file**

![conf](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/1fc6a49b-f548-4c6e-a36b-c9a1c85ba3e8)

# QOR - Quality of Results
To determine the performance of our design, we use QOR. Lets design QOR in horizontal format. Based on vendors there might be vertical format also. We can extrapolate the horizaontal format to produce vertical format QOR.

**Script for runtime**
```
set tcl_precision 3
set time_elapsed_in_us [time {exec /home/vsduser/OpenTimer-1.0.5/bin/OpenTimer < $OutputDirectory/$DesignName.conf >& $OutputDirectory/$DesignName.results}]
puts "time_elapsed_in_us is $time_elapsed_in_us microseconds per iteration"
set time_elapsed_in_sec "[expr {[lindex $time_elapsed_in_us 0]/100000}]sec"
puts "time_elapsed_in_sec is $time_elapsed_in_sec"
puts "\nInfo: STA finished in $time_elapsed_in_sec seconds"
puts "\nInfo: Refer to $OutputDirectory/$DesignName.results for warnings and errors"
```
![runtime](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/93843dfa-a5dc-4ee8-bd4b-81886b459314)

**Script for worst negative slack for output interface**

Timimg paths got violated and these negative slacks values are metioned in the results file using RAT variable. In these negative slack values, we will have the worst negative slack value which is mentioned first in the file. check the below for more clarity.

![RAT in results final](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/1a795d84-9b2c-41f1-a1e1-3589e70b5ba7)

```
set worst_RAT_slack "-"
set report_file [open $OutputDirectory/$DesignName.results r]
set pattern {RAT}
while {[gets $report_file line] != -1} {
        if {[regexp $pattern $line]} {
        set worst_RAT_slack  "[expr {[lindex [join $line " "] 3]/1}]ps"
        break
        } else {
        continue
        }
}
close $report_file
```
![rat on prompt](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/7b688921-7b26-4c87-aa04-ede8ab4a276c)

**Script for number of worst negative slack**

Lets check manually by grepping RAT from results file and do word count

![RAT count](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/68ad9612-1c9f-42f7-9fb7-7f0a2e6f5db7)

```
set report_file [open $OutputDirectory/$DesignName.results r]
set count 0
while {[gets $report_file line] != -1} {
        incr count [regexp -all -- $pattern $line]
}
set Number_output_violations $count
close $report_file
```
![no of RAT](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/73217f18-ffc7-499f-9931-15107d3be0d4)

**Script for worst setup slack**

Manually grepping the setup slack violation

![setup pattern](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/536fe852-7b1f-4e25-8827-012a349646f4)

```
set worst_negative_setup_slack "-"
set report_file [open $OutputDirectory/$DesignName.results r]
set pattern {Setup}
while {[gets $report_file line] != -1} {
        if {[regexp $pattern $line]} {
        set worst_negative_setup_slack "[expr {[lindex [join $line " "] 3]/1}]ps"
        break
        } else {
        continue
        }
}
close $report_file
```
![setup violation value](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/10136953-5c9f-449d-bc88-a278e4a11108)

**Script for number of setup slack violations**

Manually checking the setup slack violation count

![no of setup violations](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/7bac2f82-094f-4e2f-ac3a-f9ff225ed165)

```
set report_file [open $OutputDirectory/$DesignName.results r]
set count 0
while {[gets $report_file line] != -1} {
        incr count [regexp -all -- $pattern $line]
}
set Number_of_setup_violations $count
close $report_file
```
![setup no od vio](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/0d19354e-e8a3-44ab-89cb-c509df7edaa4)

**Script for worst hold slack**

Manually grepping the hold slack violation

![hold pattern](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/fd1c1b02-41b8-4d71-adba-f8fc950737bf)

```
set worst_negative_hold_slack "-"
set report_file [open $OutputDirectory/$DesignName.results r]
set pattern {Hold}
while {[gets $report_file line] != -1} {
        if {[regexp $pattern $line]} {
        set worst_negative_hold_slack "[expr {[lindex [join $line " "] 3]/1}]ps"
        break
        } else {
        continue
        }
}
close $report_file
```
**Script for number of hold slack violations**

Manually checking the hold violation count

![no of hold violations](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/8a308934-47b4-4ebc-b277-76862aa9cfe8)

```
set report_file [open $OutputDirectory/$DesignName.results r]
set count 0
while {[gets $report_file line] != -1} {
        incr count [regexp -all -- $pattern $line]
}
set Number_of_hold_violations $count
close $report_file
```
![hold vio num](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/0ed2f25d-0ea1-4930-a06d-b355f4927f16)

**Scirpt for number of instances in our design**

```
#-----find number of instance------#
set pattern {Num of gates}
set report_file [open $OutputDirectory/$DesignName.results r]
while {[gets $report_file line] != -1} {
        if {[regexp -all -- $pattern $line]} {
        set Instance_count [lindex [join $line " "] 4 ]
        break
        } else {
        continue
        }
}
close $report_file
```
![instances](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/a02ea078-379b-4246-8ac4-e23e893f206d)

**Script for quality of results datasheet**

Just for observation, lets convert QOR in horizontal format by the following script

```
puts "\nDesign_name is \{$DesignName\}"
puts "time_elapsed_in_sec is \{$time_elapsed_in_sec\}"
puts "Instance_count is \{$Instance_count\}"
puts "worst_negative_setup_slack is \{$worst_negative_setup_slack\}"
puts "Number_of_setup_violations is \{$Number_of_setup_violations\}"
puts "worst_negative_hold_slack is \{$worst_negative_hold_slack\}"
puts "Number_of_hold_violations is \{$Number_of_hold_violations\}"
puts "worst_RAT_slack is \{$worst_RAT_slack\}"
puts "Number_output_violations is \{$Number_output_violations\}"
```

![Horizontal format](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/9c374fe0-455d-4a58-8e79-eb74b2085201)

```
puts "\n"
puts "                                                       ****Quality of Results (QoR)****                                                  \n"
set formatStr {%15s%15s%15s%15s%15s%15s%15s%15s%15s}

puts [format $formatStr "-----------" "-------" "--------------" "---------" "---------" "--------" "--------" "-------" "-------"]
puts [format $formatStr "Design Name" "Runtime" "Instance Count" "WNS setup" "FEP Setup" "WNS Hold" "FEP Hold" "WNS RAT" "FEP RAT"]
puts [format $formatStr "-----------" "-------" "--------------" "---------" "---------" "--------" "--------" "-------" "-------"]

foreach design_name $DesignName runtime $time_elapsed_in_sec instance_count $Instance_count wns_setup $worst_negative_setup_slack fep_setup $Number_of_setup_violations wns_hold $worst_negative_hold_slack fep_hold $Number_of_hold_violations wns_rat $worst_RAT_slack fep_rat $Number_output_violations {
        puts [format $formatStr $design_name $runtime $instance_count $wns_setup $fep_setup $wns_hold $fep_hold $wns_rat $fep_rat]
}

puts [format $formatStr "-----------" "-------" "--------------" "---------" "---------" "--------" "--------" "-------" "-------"]
puts "\n"
puts "------------------Thank You for teaching TCL scripting-----------------"
```
* formatStr --> %15s represents a string identifier. When a string is called in $formatStr it will be automatically respaced because of the identifier in the object
* using foreach loop to enter the outputs in formatStr and generate the datasheet

**Minor modifications for observing the datasheet**

```
set Instance_count "$Instance_count sindhucell"
set worst_negative_hold_slack "$worst_negative_hold_slack negvalue88"
```
Add the above two lines of sciprt and see the change in the QOR datasheet.

![modified qor](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/e4cad1f6-3f47-488a-899e-005210f0f3ff)

## Final Quality of Results(QOR)

![QOR](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/ee63ae5e-a07c-498c-b3c2-cbc7d8935cb0)

# Conclusion
* Encapsulated the enitre design in the form of an excel sheet.
* Excel sheet was processed using TCL Box.
* TCL Box in our design is **sindhusynth.tcl**
* COnerted the inputs in the excel sheet to understandable formats of YOSYS and OpenTImer.
* Used YOSYS for synthesis and OpenTimer for generating pre-layout timing results.

# Acknowledgements
* [Kunal Ghosh](https://github.com/kunalg123)
* [Geetima Kachari](https://github.com/Geetima2021)
* [clifford wolf](clifford@clifford.at) - owner of yosys Tool
* [Tsun-Wei Huang](twh760812@gmail.com) - owner of OpenTimer Tool
