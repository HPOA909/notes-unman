# Split Mail

I like mutt.
Combined with notmuch, it's one of the best mail readers I know.
Although support for fetching and sending email has been added, at heart mutt is a great MUA.
When I started working with Qubes, one of the first things I did was to see if I could get back to the original use of mutt, and separate out the constituent parts into separate qubes.
Turns out, it's easy.

## Why?
First, it allows us to read and process mail offline in a (relatively) sealed qube.
The attack surface is small.  
Second, it allows us to easily separate fetching and sending mail streams.
We can use different Tor routes, different network interfaces, as we want.  
Third, it's fun, and we can learn a lot about the processes behind messaging, and how to organize things in Qubes.

## What?
Breaking down email processing is simple - first, you fetch mail, then you read or process it, then you send mail.  
So, we use one qube for each task.  
Obviously we want the *mutt* qube to be offline, and we can use disposableVMs as the fetching and sending qubes.
These can be firewalled appropriately to restrict network access to only required servers and ports.  
We can use an offline gpg qube to handle any encrypted messages, and any attachments can be opened in disposable offlineVMs.
This means that we protect as far as possible the mail qube from any hostile emails or attachments.
And because the mail qube is offline, we reduce the risk of our messages being leaked.

## How?
Let's start with the basics.  
Most email providers give you details of how you can collect and send mail, by specifying services with an address and port.  
For our example we will use *pop3* or *imap* to collect mail, and *smtp* to send mail.  
Our provider gives these details:  
pop3 - mail.domain.com:995
imap - mail.domain.com:993
smtp - smtp.domain.com:465

You might think of using `offlineimap` to grab the messages from the server.
There's one *huge* downside to that - it isn't possible to use offlineimap to get a subset of messages.
Although offlineimap does have `maxage`, this doesn't work with an empty Maildir.
That is, you have to sync the Maildir *first* and then run updates with the `maxage` parameter set.
And so, there will be 1 Maildir on the server, 1 copy on the "receive" qube, and another in the "mail" qube.
This seems like overkill and a waste of resources.  
You may disagree.

An alternative approach would be to use POP3 to get messages from the server.
POP3 is *often* used to delete messages from the server after they are downloaded.
This is, e.g. the default behavior in `mpop`.
This is good for us, because we don't need to worry about what's on the server: we just get the messages, and move them to the mail qube.
If we decide *not* to delete the messages, then we can limit the downloads by filtering - we get the headers, filter by date received, and then download only the recent messages.  
This is somewhat difficult, in that *all* the headers will need to be downloaded before filtering, and for messages that we **do** want to download, those headers will be downloaded twice.

Still another possibility, if one has ssh access to the server would be to use rsync on the incoming mailfile in /var/spool/mail.
This has the advantage of being absolutely straightforward, except that one cant delete the file after fetching it since there needs to be some file there for delivery to work, on the server.
It would be possible to recreate the mailfile after the rsync operation has completed.  
Of course, if the server is using maildirs, then it's simple to rsync using the `mtiime` parameter, to get most recent mails.

So there are 3 different approaches: each one has its merits, and each its difficulties.
Choose the one that works best with your server, and the way you want to use/keep your mail.
