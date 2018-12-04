# Questions
### 1. No Lock-In
What automated data export is available?
We want to be able to leave and take all our data with us. "Data" here includes: review comments, pull requests / patches (including metadata), old (rejected) pull requests and metadata, issue tracker entries and comments (if issue tracker included). This archiving should be automated, not something we do by hand.

### 2. Easy Administration
Are there any scripts or custom code required after initial setup? We would like to do as little customizing as possible.

### 3. Flexible Workflow
Can we use email patches / email review as well as pull requests / web UI review?  
  
3a. Can we can attach review comments to specific code *and* commit message locations?  
3b. Are the comments faithfully translated to notification emails (including the locations in code the comment is addressing)?  
3c. Are old topic branches (rejected or updated pull requests) available even after being rejected? (i.e. are they ever deleted?)  
3d. Is plain text supported in code review comments?   

# Phabricator
## 1. No Lock-In
All data should be sent out via email, but it appears there's no official way to 
actually _export_ the data. It's all in the database that's kept on-site, but 
would need pulled out manually. See https://phabricator.wikimedia.org/T108199 
for a discussion.
## 2. Easy Administration 
All configuration can be done either through the web interface or the scripts 
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
## 3. Flexible Workflow
Yes. You can reply to emails Phabricator sends out to add comments on the 
review, including on specific lines of code I understand.
### 3a. 
There isn't a UI to attach comments to lines in the summary, but you can 
always quote them and comment on them that way. You *can* provide comments on 
specific lines of code.
### 3b. 
Yes. For example, from one of my reviews recently, Phabricator sent:

INLINE COMMENTS

`efi_main.c:98  `
`+       if (status != EFI_SUCCESS) {  `
`+               ST->ConOut->OutputString(ST->ConOut, L"Failed to allocate memory for heap.\r\n");  `
        `BS->Exit(IH, status, 0, NULL);  `

perhaps include the size in the error message. I've found that kind of thing 
useful before.
### 3c.
Phabricator doesn't use pull requests. Tasks, Review Requests etc. are all 
kept indefinitely in the database.
### 3d.
Yes. Review comments by default use remarkup (https://secure.phabricator.com/
book/phabricator/article/remarkup/) but the default email format is plain 
text, with an option for HTML emails.  
There are also various preferences for configuring the web display. For 
example:

> Phabricator normally shows diffs in a side-by-side layout on large screens 
> and automatically switches to a unified view on small screens (like mobile 
> phones). If you prefer unified diffs even on large screens, you can select them 
> for use on all displays.


The full list of things Phabricator can do in terms of reviews, tasks, wiki 
etc. can be seen here:  
https://www.phacility.com/phabricator/   
https://code.bluestop.org/applications/query/all/  
  
The TianoCore edk2 repo that's mirrored from Github can be browsed at https://
code.bluestop.org/diffusion/EDK/ .


Note that Phabricator does *not* support pull requests as such. As they 
explain:

> Code review in Phabricator is a lightweight, asynchronous web-based process. 
> If you are familiar with GitHub, it is similar to how pull requests work:

>     An author prepares a change to a codebase, then sends it for review. They 
> specify who they want to review it (additional users may be notified as well, 
> see below). The change itself is called a "Differential Revision".
>     The reviewers receive an email asking them to review the change.
>     The reviewers inspect the change and either discuss it, approve it, or 
> request changes (e.g., if they identify problems or bugs).
>     In response to feedback, the author may update the change (e.g., fixing the 
> bugs or addressing the problems).
>     Once everything is satisfied, some reviewer accepts the change and the 
> author pushes it to the upstream.

For examples of how Phabricator works for code reviews, people can take a look 
at the following:

https://reviews.freebsd.org/differential/query/all/
https://reviews.llvm.org/differential/
https://phabricator.services.mozilla.com/differential/

