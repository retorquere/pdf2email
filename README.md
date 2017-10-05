**pdf2email** is a `Common UNIX Printing System` (`CUPS`) backend that
uses GhostScript to print a document to **PDF** and sends the final file
to a mail-to-printer service like the shitty system we have at work
here. This software is written in Python. The script was initially written by [George Notaras](http://www.g-loaded.eu/2006/12/03/pdf2email-cups-backend/); this script is a substantial rewrite by Emiliano Heyns.

## Requirements

The following software is required so that this backend functions correctly:

1.  Python 2.7 ([http://www.python.org](http://www.python.org/))
2.  CUPS server ([http://www.cups.org/](http://www.cups.org/))
3.  Ghostscript ([http://www.cs.wisc.edu/~ghost/](http://www.cs.wisc.edu/~ghost/))
4.  Email server, on which usernames from the LAN workstations either match the users on the email server or proper mappings (aliases) have been set.
4.  A mail-to-printer system like the crappy Canon systems our procurement department maintains is the best they could do.

## Installation

Put the `pdf2email` script in `/usr/lib/cups/backend/` and set the executable bit:

```
chmod +x /usr/lib/cups/backend/pdf2email
```

copy `pdf2email.conf` to `/etc/cups/pdf2email.conf` and edit as appropriate. For every user that you want to enable printing for you will have to add a `[<username>]` section.

## Configure a CUPS Printer

This section describes in brief all the required actions in order to add a printer that uses this backend to CUPS.

*   Decide the printer’s name, eg `TestPDFprinter`.
*   Decide the directory, where the PDF files are saved temporarily. This directory must be writable by the users. Usually, `/tmp` is perfect for this. Note, that no PDF files are left in this directory. This piece of information is used in the **Device URI** of your CUPS printer. For example, provided that the `/tmp` directory will be used, the Device URI would be: `pdf2email:/tmp`
*   Get a postscript printer’s PPD file. You can find such files at:
    *   Your printer **manufacturer**‘s web site.
    *   [Linux Printing](http://www.linuxprinting.org/show_driver.cgi?driver=Postscript)
    *   [Adobe’s download center](http://www.adobe.com/support/downloads/)
    *   Or you can use `email.ppd` from this repository

> **WARNING**: It is your exclusive responsibility to examine the printer driver’s license prior to using the PPD file.

Finally, add a new printer that uses this backend, by using lpadmin from the command line. As root issue the following command:

```
lpadmin -p TestPDFprinter -E -v pdf2email:/tmp -P /path/to/myprinter.ppd
```

## USAGE

Make this printer available to the LAN through the IPP, SAMBA or any other protocol and add it to your printer list in your client machines. Whatever you send to that printer will be converted to a PDF document and will be emailed to the email address in the `[To]` section.

## Trouble?

If any error occurs during the PDF creation process, there will be a failure notice in your inbox and a message in syslog (`/var/log/syslog`).

In case you receive no email after requesting a print, check syslog for any error messages. If there aren’t any, the check CUPS error log.

Finally, note that if you use a security layer like SELinux, then you might need to further adjust it so it lets 3rd party CUPS backends to function. This is beyond the scope of this document.

<!--
## PDFmarks

If you do not know what pdfmarks are, please do a web search about them. This backend can include PDFmarks in the final PDF document. Do not expect anything spectacular, as this script cannot process the document for bookmarks, links etc. What it can do is to add pre-defined pdfmarks to all PDF documents.

For example, if, for example, you want to fill some of the document properties, save the following in a file:

```
[/Title ()
/Author ()
/Subject (blah blah)
/Keywords (keyword1, keyword2, blah)
/DOCINFO pdfmark
```

By default the `Author` property is filled with the username that requested the print.

Finally, add the path to the pdfmarks file in the configuration file of the backend, as described in the configuration section:

This feature has not been tested thoroughly.

-->

## License

This project is released under the terms of the [GNU General Public License version 2 or later](https://www.gnu.org/licenses/old-licenses/gpl-2.0.html).

## Support

This software is released as _free software_ without any warranties or official support.
