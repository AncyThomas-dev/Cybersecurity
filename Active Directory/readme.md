
## Creating Domain OUs, Users, Groups

In this activity, I created the user accounts for the sales representative Bob and the developer Andrew, and created the proper organizational units and groups to put them in.

### 1. Created a top-level `GC Users` organizational unit for each department's users.

   - Right-clicked `GOODCORP.NET`, clicked `New`, then `Organizational Unit`.

   - Named this OU `GC Users`.

   - Right-clicked `GC Users` and created a new `Organizational Unit` under it, called `Sales`.

   - Right-clicked `GC Users` and created a new `Organizational Unit` under it, called `Development`.

  - We now have an `Organizational Unit` tree for our users, separated by department.


### 2. Created a user, `Bob`, in the `Sales` OU. 

  - Expanded the `GC Users` `Organizational Unit` and Right-clicked the `Sales` OU, then `New`, then clicked on `User`. A new `New Object - User` window opened up.

    - On the `New Object - User` window, typed in `Bob` for the first name and the `User logon name:`.

    - Clicked `Next >`

  - The next screen had fields for setting Bob's password and a few password settings for sysadmins to set.

    - Password - `Ilovesales!`.

    - Unchecked `User must change password at first logon`.

  - Clicked `Next >`

    - The next screen showed the settings I selected.

    - Full name: `Bob`

    - User logon name: `Bob@GOODCORP.NET`


  - 
  - Now we have a domain user account for our sales rep, Bob.


### 3. Created a group, `Sales`, within the `Sales` OU.

  - With `ADUC` still open, Right-clicked the `GC Users > Sales` OU, then click `New`, then `Group`.

    - Set the `Group name:` to `Sales`. Left `Group scope` as `Global` and `Group type` as `Security`.

    - Clicked `OK`.

  - A new group, `Sales`, appeared in the `GC Users > Sales` OU.

  - Added `Bob` to `Sales`.  

    - Right-clicked `Bob` and then `Add to a group...`.

    - In `Select Groups` typed `Sales` in the `Enter the object names to select:` field.

    - Clicked `Check Names`.

    - Clicked `OK`. A new screen appeared saying `The Add to Group operation was successfully completed.`

    - Click `OK`.

  - `Bob` is now part of the `Sales` group in our domain.

### 4. Created a user, `Andrew`, in the `Development` OU.

  - To create the `Development` user and group, used the same process used for `Sales`. 

  - Repeated the steps as Step 2 with user `Andrew`. Set the password to `Ilovedev!`.


### 5. Created a group, `Development`, within the `Development` OU. 

    - Repeated the same steps as Step 3 for used for creating the group `Development`.


6. Move the Windows 10 machine in the existing default `Computers` directory to a new `GC Computers` OU.


  - Right-clicked `GOODCORP.NET`, then `New`, then `Organizational Unit`.

  - Named this OU `GC Computers`.

  - Drag the Windows 10 VM in the existing default `Computers` directory to the new `GC Computers` OU.



