# PHP - Simple data - table system with CREATE, RETRIVE, EDIT & DELETE

## Server side database for a smaller website!
Using a SQL database for a small website is a waste of effort and speed.Here I have made a function set that can manage your data-tables in a easy and fast manner.

## Why Easy?
Whole source file is arround 20KB. **No PHP CLASSES USED** ... so easy for beginners as well.</br>

Syntax is just array like code<br/>
### Creating a file looks like this -- creating a table with fields Name,Age,Gender,Profile_Picture
// making an empty array to save the attributes<br/>
`$table = [];`<br/><br/>
//with this 'create' property,  data-file will be created at given location (any extension is supported)<br/>
`$table['create'] = 'D:/databases/file_1.sfs';`<br/><br/>
// fields (coloumns) of the table --- can not change after creation<br/>
`$table['fields'] = ['name','age','gender','profilePic'];`<br/><br/>
//proceeding the task --- the command to run the SFS function to do what ever you have set in $table<br/>
`runsfs($table);`<br/><br/>
//**optional**... If you really forcefully need to make a new blank table with given fields, set this to **true**(otherwise nothing will happen, if there is a file at the given location).Make sure this is set before the **runsfs();**.<br/>
`$table["overwrite"] =true; `<br/><br/><br/>
### Adding records looks like this
//first make a empty array always.otherwise some settings from previous processes will affect your current poccess!<br/>
`$table =[];`<br/>
//now set the table we need to consider --- If file not found=>nothing will happen:not even a error is given..so be careful<br/>
`$table['table'] = 'D:/databases/file_1.sfs';`
//adding each record this way...looks long code at first time but this looks so clear/readable!<br/>
`$table['add']['name'] = 'John Doe';`<br/>
`$table['add']['age'] = 21;`<br/>
`$table['add']['gender'] = 'M';`<br/>
//This is a example that you can save image files as well (as a binary file...it will be saved in a **/binary** folder).The **binary** setting should be set to true(as shown below)..otherwise it will try to save the image file in to the text-data-table-file and will surely crash!
<br/>
`$table['add']['profilePic'] = $profilePictureContent;`<br/>
//**This will set that profilePic fieled to save externally**<br/>
`$table['binary']['profilePic'] = true;`<br/>
//proceeding the task --- remember to keep all the settings set before this!<br/>
`runsfs($table);`<br/><br/>
<br/>
//You dont have to worry about the **/binary** folder everything about that is taken care by the code.You just have to request/retrive the file then the code will return the image file as you had in the first place.<br/>**But it is not a good practice to play with binary/image/large files inside PHP.** It will slower the pages!So try to save file names in the tables instead of whole files.<br/><br/>


### Adding multiple (3 in this example) records at once
//Replace with $table['add']<br/>
`$table["addmultiple"] = [`<br/>`array('name'=>'rekieg','age'=>'21','gender'=>'M','profilePic'=>$photoBinary1),`<br/>
                         `array('name'=>'jayani','age'=>'23','gender'=>'F','profilePic'=>$photoBinary2),`<br/>
                         `array('name'=>'alehandro','age'=>'25','gender'=>'M','profilePic'=>$photoBinary3)`<br/>`];`<br/><br/>
                     


### Getting data back from the table (Retriving)
Actually we have created a table.So we have many records with data.Therefore **getting data back is a LOOP within each record(row).**
<br/>
<br/>

`$table = [];` // clearing previous stored data<br/>
`$table['table'] = 'D:/databases/file_1.sfs';` // select the table
<br/>
 // function to do each row --- can use any variable name for $record .... it will become a array containging each rows's data<br/>
`$table["function"] = function($record)` <br/>
   `{`<br/>
       `echo $record['name'] . ' is ' . $record['age'] . ' old <br/>';`<br/>

   `}`<br/>
   `runsfs($table);`<br/><br/>
**Function will run from top to bottom.You can not change any record in this method.Read Only!**<br/>
### To do the function bottom to top (reverse) ... set setting 'reverse' to true 
`$table["reverse"] = true;`//this will not change the table..only the order of loop..make sure these settings are before **runsfs()**<br/><br/>

### How to stop the loop at middle?
Now we know loop from top to bottom and bottom to top.<br/>
But if we need to stop looping after finding something!?<br/>
**Imagine we have a login_data_of users .. and we need to find the match for given email($email)  and password ($password).**<br/>
So after a match we do not need to search for more...that means we have to stop in middle!<br/>
`$table['function'] = function($user)`<br/>
`{`<br/>
        `if($user['email'] == $email && $user['password'] == $passsword)`<br/>
        `{`<br/>
        //match found<br/>
        `echo 'match found';`<br/>
        `$GLOBALS['stillMatchNotFound'] = false;`<br/>
        `}`<br/>
`}`<br/>
`$table['*while'] = 'stillMatchNotFound';`<br/>
`$GLOBALS['stillMatchNotFound'] = true;`// --------------------------------(#1)<br/>
`runsfs($table);`<br/><br/>

**EXPLANATION**<br/>
with this \*while setting we can stop the loop at middle...The loop will only run until the $GLOBAL variable named as \*while is true.You have to set $GLOBAL variable to true at the beginning (SHOWED IN #1)!Otherwise the loop will nerver happen at all!<br/>
**You might wonder why there is a star/asterisk at the front of while?** That is because this \*while is just the GLOBAL variable name to find out the true or false value...SO it is like a pointer in C++..That is why a \* is used!

## Dangerous --- MODIFYING : changing and deleting 
Until you are not familiar with code please keep back up of tables before trying to change or delete.It is a well known fact that changing database is a **very risky** task.**There is no undo!**


### Type - 01 modifying  - same change for every selected : row/record
1) If you need to change users' **'maturity'**-field to **'mature'** if their **'age'** is 18 or larger.<br/>
2) If you want to delete every user , who's  **age** less than 18<br/>
In both (1),(2) we select some users THEN do the same change to each of them.That means both changing to 'mature' and deleting is done to all/everyone selected.The action we take does not depend on the current value of user's data.(user data is taken only to select...not to decide what to put in their fields).<br/><br/>
3)Adding 20% to each students' English marks if their current marks are less than 50<br/>
In (3) we select some students as before..But we also need the current marks to calculate the 120% and set it as English marks.**We will say this is a typ e 2 modification**


<br/><br/>
### Type 01 modify --- easy!
//changing as in (1)<br/>
`$table = [];` // clearing previous settings<br/>
`$table['table'] = 'D:/files/users.sfs';`<br/>
`$table['if'] = function($user){return ($user['age'] >= 18);};` //selecting required records to change<br/>
`$table['change']['maturity'] = 'mature';`//each and every one who need the change.It is changed to exact same thing : 'mature'<br/>
`runsfs($table);`<br/><br/>
//deleting as in (2)<br/>
`$table = [];` // clearing previous settings<br/>
`$table['if'] = function($user){return ($user['age'] < 18);};` //selecting required records to DELETE<br/>
`$table['delete'] = true;`//Everyone selected is deleted.<br/>
`runsfs($table);`<br/><br/>
<br/><br/>
### Stop changing/deleting at middle
You can stop changing at middle :s ame as in LOOPING the function...just use \*while and make it false when ever you want to stop.!<br/>
`$table['*while'] = 'keepChangingORDeleting';`<br/>
`$GLOBALS['keepChangingORDeleting'] = 1;//in PHP 1(or larger values) are true....0 is false.....we can use this to make code easy!`<br/>
### EX : IF you want to delete only max 5 users from selected
//
`$table['if'] = function($user)`<br/>
`{`<br/>
`        if($user['age']<18)//if age is less than 18`<br/>
`        {`<br/>
`        countsfs('moreToDeleteCount'); // we have found one! countsfs reduces the $GLOBALS['moreToDeleteCount'] by 1`<br/>
`       return true;//this is selected to do the change (change = delete in this example)`<br/>
`        }`<br/>
`};`<br/>
`$table['delete'] = true;//   delete the user for selected`<br/><br/>
`// 5 in php is :true .. with countsfs() we decrement this until become 0:which is false..So changing/deleting will stop!`<br/>
`$GLOBAL['moreToDeleteCount'] =5;`<br/>
`$table['*while'] = 'moreToDeleteCount'; `<br/>
<br/>
`runsfs($table);//do the thing`<br/>

### Type 02 - MODIFYING - dynamicaly  - (case 3) is shown as an example
`$table['table'] = 'D:/files/students.sfs';`//file with students marks<br/>
`$GLOBALS['newEnglishMarks'] =0;`//this is going to keep the calculated new marks temporaly for each record<br/>
`$table['if'] = function($student)`<br/>
`{`<br/>
       `  if ($student['englishMarks'] < 50)`//selection<br/>
        ` {`<br/>
       `  $GLOBALS['newEnglishMarks'] = $student['englishMarks'] * 1.2;`//assigning new marks<br/>
        ` return true;`//marking this student as need to change<br/>
       `  }`<br/>
`};`<br/>
`$table['*change']['englishMarks'] = 'newEnglishMarks';`//setting the calculated marks<br/>
//notice the asterisk infront \*change .... that is because it has the pointer (name of the $GOBAL variable) which is going to give the modified value for each record/row/student/user<br/>If this asterisk is not there(change instead of \*change) it will be type 01 modification...all selected english marks will be replaced with string 'newEnglishMarks'..And then **there is no UNDO**..so be careful about coding errors.**Keep back up in the first days!**
<br/>
`runsfs($table);`//do the things<br/><br/>


## countsfs($globalVariableName,$weight=1)
Used to count backward until 0 is given<br/>
Minimum value given id 0:which is false
<br/>
First parameter is the name of global variable..not the global variable.it should be its name ("which is set ast \*while:it is the name/pointer)<br/>
Second parameter is weight..that is how much should we decrement as one count<br/>
Example:<br/><br/>
`$GLOBAL['a'] = 14;`<br/>
`countsfs('a',4);`// now $GLOBAL['a'] = 10;<br/>
`countsfs('a',7);`//$GLOBAL['a'] = 3;<br/>
`countsfs('a',4);`//$GLOBAL['a'] = 0 ............ not -1 ..it never go below 0 (it reduces the given variable until that become false:that is 0);<br/><br/>

## runsfs($tableDataArray)
Just make sure all the settings are defined/set to the tableDataArray before running this.<br/>
If your code does not do what you expect check this fact as first thing!

# That's all
If you have any issue about code: **djpazndu+sfs@gmail.com**























