This page documents our source control commit message format.

This format is also documented in [Contributions.txt](https://github.com/tianocore/edk2/raw/master/Contributions.txt) which may be available in the source tree as well.

Use this format for commit messages, and when providing the log message for a patch.

# Commit message format

<pre>
Pkg-Module: Brief-single-line-summary

Full-commit-message

Signed-off-by: Contributor Name &lt;contributor@email.server&gt;
</pre>

Where:
* Pkg-Module is the EDK II Package and optionally the Module
* Brief-single-line-summary is a short summary of the change
* The length of 'Pkg-Module: Brief-single-line-summary' should be less
  than 72 characters
* CVE fix needs to append CVE number in Brief-single-line-summary. The format is 'Pkg-Module: Brief-single-line-summary (CVE-Year-Number)'. Its length should be less than 92 characters.
* blank-line is an empty line
* Full-commit-message is the full message describing the change. Its first lines are bugzilla URL if there are one or more bugzilla.
* Line length should be less than 76 characters when possible
* Signatures is one or more lines with signatures. Please see the
  [[Commit Signature Format]] page for more information.
* The entire log message should use only standard ASCII text
  characters

An example would be:
<Pre>
Package/Module: Short one line description of change

REF:https://bugzilla.tianocore.org/show_bug.cgi?id=1000

Several lines of
description for the
change.

Signed-off-by: Contributor Name &lt;contributor@email.server&gt;
Reviewed-by: Reviewer Name &lt;reviewer@reviewer-email.server&gt;
</Pre>

A CVE example would be:
<Pre>
Package/Module: Short one line description of change (CVE-2018-12180)

REF:https://bugzilla.tianocore.org/show_bug.cgi?id=2000

Several lines of
description for the
change.

Signed-off-by: Contributor Name &lt;contributor@email.server&gt;
Reviewed-by: Reviewer Name &lt;reviewer@reviewer-email.server&gt;
</Pre>

# See Also
* [[Code-Style]]
* [[Oneline Release Notes Generation]]
