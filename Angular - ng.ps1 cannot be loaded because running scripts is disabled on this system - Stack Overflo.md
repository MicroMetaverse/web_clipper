# Angular - ng.ps1 cannot be loaded because running scripts is disabled on this system - Stack Overflow
In my Angular-12 Application, I tried to generate service using:

> ng g s auth/services/login

but I got this error:

```
ng : File C:\Users\akweey\AppData\Roaming\npm\ng.ps1 cannot be loaded because running scripts is disabled on this system. For more 
information, see about_Execution_Policies at https:/go.microsoft.com/fwlink/?LinkID=135170.
At line:1 char:1
+ ng g s auth/services/login
+ ~~
+ CategoryInfo          : SecurityError: (:) [], PSSecurityException
+ FullyQualifiedErrorId : UnauthorizedAccess

```

When I checked the path: C:\\Users\\akweey\\AppData\\Roaming\\npm\\ng.ps1,

I only found ng but no ng.ps1

How do I get this resolved?

Thanks

asked Jun 29, 2021 at 11:47

\[

![](https://www.gravatar.com/avatar/8527a0dfa4110e2db92e660a96daee9e?s=64&d=identicon&r=PG&f=1)

]([https://stackoverflow.com/users/11352561/user11352561](https://stackoverflow.com/users/11352561/user11352561))

[user11352561](https://stackoverflow.com/users/11352561/user11352561)user11352561

1,6596 gold badges28 silver badges58 bronze badges

This issue occurs due to undefined Execution policy.

Try this command in Powershell:

```
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy Unrestricted

```

answered Jun 29, 2021 at 11:56

\[

![](https://lh3.googleusercontent.com/-XdUIqdMkCWA/AAAAAAAAAAI/AAAAAAAAAAA/4252rscbv5M/photo.jpg?sz=64)

]([https://stackoverflow.com/users/9618378/drashti-dobariya](https://stackoverflow.com/users/9618378/drashti-dobariya))

[Drashti Dobariya](https://stackoverflow.com/users/9618378/drashti-dobariya)Drashti Dobariya

1,4361 gold badge7 silver badges17 bronze badges

Just delete this file. C:\\Users\\username\\AppData\\Roaming\\npm\\ng.ps1

answered Dec 12, 2021 at 14:12

\[

![](https://lh3.googleusercontent.com/a/AATXAJxgdCBdXjVIFuxFpjKL0Osp04a2xUvI5c8kluY5=k-s64)

]([https://stackoverflow.com/users/17658465/gbroz-dev](https://stackoverflow.com/users/17658465/gbroz-dev))

execute this on powershell,

```
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy Unrestricted

```

for next, "Do you want to change the execution policy?" give "yes to all"

```
A

```

answered Nov 29, 2021 at 4:54

\[

![](https://i.stack.imgur.com/PEuTz.jpg?s=64&g=1)

]([https://stackoverflow.com/users/9439677/sujeewa-k-abeysinghe](https://stackoverflow.com/users/9439677/sujeewa-k-abeysinghe))

[![](https://i.stack.imgur.com/jdbGH.png)
](https://i.stack.imgur.com/jdbGH.png)

I have installed the ng (angular in globally) and trying to access in E:\\ drive got above error, I executed the above given command, then now working fine for me.[![](https://i.stack.imgur.com/ZZsKj.png)
](https://i.stack.imgur.com/ZZsKj.png)

answered Aug 15, 2021 at 3:10

\[

![](https://i.stack.imgur.com/4RNcU.jpg?s=64&g=1)

]([https://stackoverflow.com/users/6270984/g-krishna](https://stackoverflow.com/users/6270984/g-krishna))

[G Krishna](https://stackoverflow.com/users/6270984/g-krishna)G Krishna

811 silver badge4 bronze badges

Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy Unrestricted Try to run the above command. This worked for me.

answered Dec 16, 2021 at 14:11

\[

![](https://lh5.googleusercontent.com/-E5BBwTivVYc/AAAAAAAAAAI/AAAAAAAAABc/SR40FxLe8DA/photo.jpg?sz=64)

]([https://stackoverflow.com/users/9293306/akash-sahu](https://stackoverflow.com/users/9293306/akash-sahu))

Not the answer you're looking for? Browse other questions tagged [angular](https://stackoverflow.com/questions/tagged/angular "show questions tagged 'angular'") or [ask your own question](https://stackoverflow.com/questions/ask).

* * *

 [https://stackoverflow.com/questions/68178101/angular-ng-ps1-cannot-be-loaded-because-running-scripts-is-disabled-on-this-sy](https://stackoverflow.com/questions/68178101/angular-ng-ps1-cannot-be-loaded-because-running-scripts-is-disabled-on-this-sy)
