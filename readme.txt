referralSourceAutomationFromVBA v0.1.1

Written by Tim Lightfoot, TLightfoot@ntst.com, tlightfo@gmail.com


Version history

07/24/2014 v0.1.0
	Initial build

07/30/2014 v0.1.1
	Added unblocking the WASP.dll after copying it from the network drive.


Dependencies
	This script requires that PowerShell 3.0 be installed. At the time this is being written 3.0
	the newest version of PowerShell. If a newer version of PowerShell is being used, there is no
	guarantee that this script will work.


What is it?
	This is a PowerShell script that automates the referral source entry process. This version is
	started from a button in the PF PM Data Collection Workbook.


How is it used?
	The first step in automated data entry is validating the user entered data. To validate 
	the data go to the Validator sheet toward the end of the Data Collection Workbook. Click the
	button at the top labeled 'Run Validator' listed under Step 1. This will run through all of 
	the importable sheets in the workbook and verify that all of the user entered data matches
	the criteria for Avatar dictionaries. After the validator has run it will report any errors
	to the user by listing them on the Error_Report sheet that it generates. If there are no 
	errors listed for guarantors, this script is ready to be run.

	To run this script file go to the Validator sheet and click on the button labeled 'Run 
	automated referral source entry'. This will open a PowerShell window which will display the
	scripts	actions.

	After the script has run for a few seconds it will display a message box instructing the user
	to open Avatar and open the Referral Source Maintenance form. Once the user clicks on OK in
	this message box, the script will take over use of their computer. It uses the tab order of
	Avatar to navigate through the Referral Source Maintenance form so it requires that Avatar
	remain the active window throughout the automation process. This script will take around 
	fifty-five seconds to enter in one referral source.

	Once the script has completed the data entry it will display a message box informing the user
	that the process is complete. The user is now free to use their computer again.


How does it work?
	In order for this script to run it requires that the WASP PowerShell module be imported. WASP
	is a PowerShell module that allows for the emulation of key-presses and mouse events, this is
	how we are interfacing with Avatar. The WASP module is located on the N: drive at
	N:\ProjectManagement\Share\Plexus\Plexus_Tools\WASP. If the user doesn't have the WASP module
	copied to their local machine, the script will locate it on the N: drive, make the
	MyDocuments\Windows PowerShell\WASP\ directory, copy the WASP.dll into the new directory, and
	unblock the .dll so it can be used on the users system. It then imports the module to enable 
	PowerShell to use WASP commands.

	Next the script opens the workbook from which the script was called. It knows the workbook 
	which opened it due to the script accepting a command line argument. When the script is called
	in VBA it is called with the full path of the workbook in which the 'Run 
	automated referral source entry' button was pressed. PowerShell doesn't care that the workbook
	is already open as it is only reading data from the workbook, not writing anything. If there
	is an error opening the workbook, the user will be informed and the script will exit safely.

	After the workbook has been opened the script will locate the macroReferral_Source worksheet.
	This sheet is generated during the validation process, which is one reason why the workbook
	needs to be validated before importing. To look for the macroReferral_Source sheet the script
	looks through all of the sheets in the workbook starting at the first tab.This is slow but it
	allows some flexibility in the sheets location. If more sheets were added to the workbook, the
	location of the macroReferral_Source sheet may change and it would not maintain the same
	index. By looking at all of the sheets we can find the macroReferral_Source sheet no matter
	it's location in the workbook.

	PowerShell is extremely slow in reading data from an open Excel sheet. In order to bypass this
	slow read speed the script saves the macroReferral_Source sheet as a .csv file in the user's
	temp directory. This allows the script to use PowerShell's built in Import-Csv function which
	loads the list of guarantors in a fraction of second from the temporary .csv file, rather than
	several	seconds from the Excel file. After creating the .csv file the script will close the
	workbook, load the .csv file into memory, and remove the .csv file as it is no longer needed.

	It will then prompt the user for action. It will display a message box instructing the user
	to open Avatar and open the Referral Source Maintenance form and to make sure that this is the
	only form open. This is necessary due to the tab order changing if multiple forms are open.
	If the user presses 'Cancel' the script will exit safely. If the user presses 'OK' the script
	will take take control of the keyboad and begin the automated referral source entry.

	The script locates Avatar by it's process name, 'javaw', in Task Manager. The script assumes
	that it is being started in the same place every time it is run, in fact, it is absolutely 
	necessary that the Referral Source Maintenance form is open and the 'Add Referral Code' radio
	button is highlighted before the script is run. The scripts first step is to choose the 'Add
	Referral Code' button so it needs to start from that same place every time. This means that
	if the user were to open the Referral Source Maintenance form and click something, it would
	throw off the starting position of the script and it will not work. However, due to this being
	a "dumb program" it is not getting any feedback from Avatar and it will assume everything is 
	fine and continue through the tab order and data entry until it runs out of referral sources 
	to enter. If this occurs, the program will need to be killed and restarted at the right place.
	
	In order to stop the automated process for any reason, the PowerShell window will need to be
	closed. This may be difficult due to the script selecting Avatar as the active window on every
	command it sends (roughly every second). This will make it near impossible to select
	PowerShell and exit out of it in time. It is recommended to move the PowerShell window to a 
	separate monitor to make it easy to locate. 