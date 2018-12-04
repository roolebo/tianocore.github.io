# Questions
1. No Lock-In - What automated data export is available?
We want to be able to leave and take all our data with us. "Data" here includes: review comments, pull requests / patches (including metadata), old (rejected) pull requests and metadata, issue tracker entries and comments (if issue tracker included). This archiving should be automated, not something we do by hand.

2. Easy Administration - Are there any scripts or custom code required after initial setup? We would like to do as little customizing as possible.

3. Flexible Workflow - Can we use email patches / email review as well as pull requests / web UI review?**
  3a. Can we can attach review comments to specific code *and* commit message locations?
  3b. Are the comments faithfully translated to notification emails (including the locations in code the comment is addressing)?
  3c. Are old topic branches (rejected or updated pull requests) available even after being rejected? (i.e. are they ever deleted?)
  3d. Is plain text supported in code review comments? 

# Phabricator
1. All data should be sent out via email, but it appears there's no official way to 
actually _export_ the data. It's all in the database that's kept on-site, but 
would need pulled out manually. See https://phabricator.wikimedia.org/T108199 
for a discussion.
2. All configuration can be done either through the web interface or the scripts 
in the 'bin' directory - e.g.:

./bin/config
./bin/storage
./bin/repository

 An example of using 'config':

> % ./bin/config get phabricator.base-uri  
> {  
>   "config": [  
>     {  
>       "key": "phabricator.base-uri",  
>       "source": "local",  
>       "value": "https://code.bluestop.org",  
>       "status": "set",   
>       "errorInfo": null  
>     },  
>     {  
>       "key": "phabricator.base-uri",  
>       "source": "database",  
>       "value": null,  
>       "status": "unset",  
>       "errorInfo": null  
>     }   
>   ]  
> }  
3. Yes. You can reply to emails Phabricator sends out to add comments on the 
review, including on specific lines of code I understand.
3a. There isn't a UI to attach comments to lines in the summary, but you can 
always quote them and comment on them that way. You *can* provide comments on 
specific lines of code.
3b. 
Yes. For example, from one of my reviews recently, Phabricator sent:

INLINE COMMENTS

> efi_main.c:98  
> \+       if (status != EFI_SUCCESS) {  
> \+               ST->ConOut->OutputString(ST->ConOut, L"Failed to allocate memory for heap.\r\n");  
> BS->Exit(IH, status, 0, NULL);  

perhaps include the size in the error message. I've found that kind of thing 
useful before.

