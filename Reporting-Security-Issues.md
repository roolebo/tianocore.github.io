# How to report a Security Issue

The bug tracking system used for Tianocore projects is [Tianocore Bugzilla](https://bugzilla.tianocore.org).  An account must be [created](https://bugzilla.tianocore.org/createaccount.cgi) to enter a new issue or update exiting issues.  New security issues must be entered using the **Tianocore Security Issue** product.  Issues in the **Tianocore Security Issue** product are only visible to the **Reporter** of the issue and the members of the **infosec** group.  In your report please include the paths of the modules you believe are involved and a detailed description of the issue.

Additional information about Tianocore Bugzilla can be found in [Reporting Issues](Reporting-Issues "wikilink")

# How Security Issues are Evaluated

When a Tianocore Security Issue is entered, the issue is evaluated by the **infosec** group to determine if the issue is a security issue or not.  If it is not deemed to be a security issue, then the issue is converted to a standard issue and follows the normal issue resolution process.   If the issue is confirmed to be a security issue, then the priority, severity, and impact of the issue is assessed by the **infosec** group.  Discussions, resolution, and patches are completed within Bugzilla.  A date for public disclose is determined, and on that date the issue is made public and added to the list of Security Advisories.

If you are interested in being involved in the evaluation of Tianocore Security Issues, then please send an email request to join the Tianocore Bugzilla infosec group to the Tianocore Community Manager or one of the Tianocore Stewards.

**NOTE**: Never send any details related to a security issue in email.

Also, tianocore infosec team members should only share details of unmitigated issues in the infosec-tagged Bugzilla entries. Any sharing of unmitigated issues on un-encrypted email or open source prior to embargo expiry may lead to removal from the infosec group.

The tianocore infosec team uses the following flow to evaluate items
![](https://github.com/jwang36/tianocore.github.io/raw/master/security/flowchart.svg?sanitize=true)


# Security Advisories

List of current EDK II Security Advisories can be found at this Gitbook : 
**[Security Advisory Log]( https://www.gitbook.com/book/edk2-docs/security-advisory/details)**



