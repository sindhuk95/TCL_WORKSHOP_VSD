![grep input and output](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/7ed27215-0b77-42a5-98c4-06bd8d5b55c2)![tmp files after output delays](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/3a608609-3cd3-46b9-8b81-47cd85a9eba0)![first_config_file](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/826d346b-2b33-4838-a3a6-9b7a567e84df)# TCL_WORKSHOP_VSD
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

In this course we implement TCL script which is in the bottom line of the shell script **(sindhusynth)**. The below image is content of TCL Box.

![sindhusynth](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/7b0b0303-f7fd-486d-ba73-69a32050bc23)


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

![synth log](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/d2fabfe1-c989-467b-9421-a44f205a0042)

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

![scn1](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/1c09c1d4-f163-49d7-9ca4-76aa22f9d87d)

### Scenario 2 - Where user passes an argument that is not present in our design.

Code for this in the file is

```
f (! -f $argv[1] || $argv[1] == "-help") then
            if ($argv[1] != "-help") then
                 echo "Error: cannot find csv file $argv[1]. Exiting..."
                 exit 1
```
When i gave an incorrect argument like below, the above code gets executed and displays the error on the command prompt

![scen 2](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/298981c6-4a3c-47f1-9db5-d986ed2d1617)

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

![scn3](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/d970e25e-da72-4f46-8b75-ad99527cbeac)


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

![auto creation of variables](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/5d409a4f-c6d6-4a3b-8c34-358d671111eb)

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

![files and driectories exits](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/5bd8c778-d98c-47e6-94b1-11b859dfe15e)

when user gives a wrong path for files, an error will be displayed

![error for missing fdiles or wrong paths](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/51f5f82d-6a7e-4576-80d6-7589657eaedb)


Few Steps before reading the constraint file is

1. Dump the constraints
2. read the file to matrix called m, read2matrix
3. link the matrix to the array **my_arr(colu,rows)**
4. string mapping

![dumping constraints](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/d8a1114b-ed82-4592-9d82-762153069bdd)

Inorder to access the constraint file, we need to know the columns and rows for clocks,inputs and outputs and process them

![cio rows and columns](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/10d5a3de-9c6d-4106-a687-5c12345553de)

![rows and columns](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/743948aa-0611-49e8-ab0c-98b0a3c83353)

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
![clock constraints](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/d0bb6aa7-b22e-49d3-9c35-71df838de258)

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

![clock constraints 2](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/512714e2-2d48-4e17-af31-9e60647fe645)

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
![output input process](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/88dc621e-de92-4509-b5ed-9d2d4404e0b9)

**Input Ports created in sdc file**

![sdc file inputs](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/1da2b5b1-365d-4ff1-9e58-2f1cbddcfd5c)

Grep the input port names just for observation

![input port name](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/74f17a3e-3ef3-40e5-807b-d58c697cfedb)

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

![output output process](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/b83c7688-c606-4d5e-ad4c-a2c0b708648f)

Output port names are created in sdc files

![sdc out files](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/65375fdd-309d-4274-b17b-fd600dae78b1)

Grep the output port names for observation

![output portname](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/46143e8d-e31c-416b-bb92-36ea08c3c244)

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
![hierarchy err_flag 0](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/30f0e526-78f8-4494-ba81-aeac25bb7053)

**Error Handling** : reference module error due to incorrectly coded or module doesnt exist 

![hierarchy err_flag 1](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/3ab2da81-38c6-4ba9-877a-e096bad47127)

**Error in the error log file**

![error in log file](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/6b442eaf-3844-4532-a984-9f71bcee30b5)

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
![synthesis success](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/d311e05f-e793-4d9e-b2c3-3a483727673d)

## Synthesised Netlist

![final synth again](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/cfea718f-db00-4aa9-b3e7-dd625bd4922a)

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

![new start file with count](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/713b2882-be13-4a76-8c33-6287cf3793de)

### Netlist after removing *s and \s

![final synth file](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/ea7cbb1f-b512-4ff0-bc75-39a27d2228d3)

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
![first_config_file](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/071522ea-980d-4004-8e43-4cfd8bf9aa4d)

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
![lib conf](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/e980a34d-02c0-4fd8-aee3-3e65fec8485d)

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

![verilog file](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/7d3664d4-656a-4c4a-80fd-9fa9c7c1ca31)

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
![sdc name](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/2082255f-7dad-4cef-b58c-49dc835827d8)

sdc files after removing brackets

![sdc file with removed square brackets](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/b07bc472-18c3-426a-a18d-91bd8b5589f1)

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
![clock constraints](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/aac179c4-20fb-4186-8f6c-9e62645c52a8)

tmp file tmp1 has intermediate data i.e., sdc file with removed brackets and tmp3 has final timing file which in this stage have clock constraints required for opentimer

![sdc file with removed square brackets and clock format](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/55cc09dd-e19e-48ca-b823-3c22458ae78e)

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
![clock latency](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/b23b4472-86f4-42c5-af6d-25dad1770665)

tmp file tmp2 has intermediate data i.e., delay values and tmp3 has final timing file which in this stage have clock and its latency constraints required for opentimer

![tmp files after clock latency](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/e12c71ca-b2ad-4733-8392-3330049ce7d5)

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
![clock transition](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/0b2f88ac-b187-47cc-9dee-c4bef2d05d91)

tmp file tmp2 has intermediate data i.e., clock transition i.e., slew values and tmp3 has final timing file which in this stage have clock constraints required for opentimer

![tmp files after transition](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/5ab452cb-e893-44c2-b01f-3be03c42ec7e)

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
![input delay constra](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/57d41807-63a5-48b8-83b4-bd71f94a51f9)

tmp file tmp2 has intermediate data i.e., input delays and tmp3 has final timing file which in this stage have clock constraints, input delays required for opentimer

![tmp files after input](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/877dfbd5-1651-42b2-b17b-e613e1ab754d)

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
![input slew](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/d6101657-b3e8-4df7-b76c-9db185d4d3e3)

tmp file tmp2 has intermediate data i.e., input transition i.e., slew values and tmp3 has final timing file which in this stage have clock and input constraints required for opentimer

![tmp files after total input](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/e47e174a-3674-4b3a-997d-fc3c33faad89)

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
![output delay](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/7ed66532-f3ce-4aea-a746-bcb6abc0edd3)

tmp file tmp2 has intermediate data i.e., output delays and tmp3 has final timing file which in this stage have clock and input constraints, output delays required for opentimer

![tmp files after output delays](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/72eec5fd-0e46-4399-902b-d98d9dcff776)


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
![output load](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/0193b575-8fea-4119-b7f3-d75d25bddc16)

tmp file (tmp3) has final timing file which in this stage have all the clock, input and output constraints required for opentimer

![tmp files after output](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/54294757-4e99-435d-80d7-28fe0efcad50)

check for bussed input and output ports in tmp3 file

![stars in tmp3 file](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/c19c3a0e-6ef5-4e0f-b8bb-c5784d9afe2b)

Convert these bussed ports into bit blasted ports by using netlist. Check the bit blasted ports in netlist using grep.

![grep input and output](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/a5406163-4c3b-485a-9266-27cce73c09c1)

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
![bussed to bit ports](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/42d855f9-3eff-4b55-bca3-5ab9424c1ee2)

lets check what tmp3 file has

![tmp file with bussed ports](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/50877a42-b275-496a-b43d-0fa73f061996)

These bussed ports are converted to bit blased ports in the timimg file

![bussed to bit blasted ports in timimg file](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/22e5a580-92ab-48be-84fe-2bac5481c0c8)

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

![spef](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/3fe3e7aa-c10b-48d2-8031-39e362ac8652)

**Conf file**

![conf](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/30d765d5-3339-42cf-b6d9-3f1330c9db1e)

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
![runtime](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/30e5cb63-5641-49f1-9f5d-7e542dfbf0cf)

**Script for worst negative slack for output interface**

Timimg paths got violated and these negative slacks values are metioned in the results file using RAT variable. In these negative slack values, we will have the worst negative slack value which is mentioned first in the file. check the below for more clarity.

![RAT in results final](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/03402812-2b66-465b-9438-7122ccf26210)

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
![rat on prompt](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/bf567201-db83-4367-ad36-ccf76f75e7a4)

**Script for number of worst negative slack**

Lets check manually by grepping RAT from results file and do word count

![RAT count](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/008fea5c-455b-4a50-b4a2-2b77b2e7b8bc)

```
set report_file [open $OutputDirectory/$DesignName.results r]
set count 0
while {[gets $report_file line] != -1} {
        incr count [regexp -all -- $pattern $line]
}
set Number_output_violations $count
close $report_file
```
![no of RAT](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/4dafdc8b-a2d9-450e-be6a-307cfa7f5ced)

**Script for worst setup slack**

Manually grepping the setup slack violation

![setup pattern](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/331b4855-4f6d-4ba8-bf22-53f67572b8f5)

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
![setup violation value](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/3adb85dc-46d6-43cb-b46f-b88db2e2eaf5)

**Script for number of setup slack violations**

Manually checking the setup slack violation count

![no of setup violations](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/d073645e-f6b1-4236-961c-7f893ecad711)

```
set report_file [open $OutputDirectory/$DesignName.results r]
set count 0
while {[gets $report_file line] != -1} {
        incr count [regexp -all -- $pattern $line]
}
set Number_of_setup_violations $count
close $report_file
```
![setup no od vio](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/e12362ee-fef7-4c60-a984-0b1b9ad62527)

**Script for worst hold slack**

Manually grepping the hold slack violation

![hold pattern](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/17dab773-3db0-4c35-a754-b367695a57df)

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

![no of hold violations](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/6e17802b-e8ad-42ca-9d56-d2df3023f2e2)

```
set report_file [open $OutputDirectory/$DesignName.results r]
set count 0
while {[gets $report_file line] != -1} {
        incr count [regexp -all -- $pattern $line]
}
set Number_of_hold_violations $count
close $report_file
```
![hold vio num](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/bdf00123-b131-4149-8ef4-e2f4668c8225)

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
![instances](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/ea8a8f34-61ae-4370-a9af-07b4e4dabab2)

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
![Horizontal format](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/c9a5938a-21a9-42c7-8440-db6fe6ea93b1)

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

![modified qor](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/a4721bec-eba6-48c5-b6bc-a6ceae2c6e4b)

## Final Quality of Results(QOR)

![QOR](https://github.com/sindhuk95/TCL_WORKSHOP_VSD/assets/135046169/8a00042d-5b50-40c9-9b7f-c4f3172581f4)

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
