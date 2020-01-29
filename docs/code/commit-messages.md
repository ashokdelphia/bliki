# Writing Good Commit Messages

Rule zero: “good” is defined by the standards of the project you're on. Have a look at what the existing messages look like, and try to emulate that first before doing anything else.

Having said that, here are some principles I've found helpful and broadly applicable.

* Treat the first line of the message as a one-sentence summary. Most SCM systems have an “overview” command that shows shortened commit messages in bulk, so making the very beginning of the message meaningful helps make those modes more useful for finding specific commits. _It's okay for this to be a “what” description_ if the rest of the message is a “why” description.

* Fill out the rest of the message with prose outlining why you made the change. Don't reiterate the contents of the change in great detail if you can avoid it: anyone who needs that can read the diff themselves, or reach out to ask for help understanding the change. A good rationale sets context for the problem being solved and addresses the ways the proposed change alters that context.

* If you use an issue tracker (and you should), include whatever issue-linking notes it supports right at the start of the message, where it'll be visible even in summarized commit logs. If your tracker has absurdly long issue-linking syntax, or doesn't support issue links in commits at all, include a short issue identifier at the front of the message and put the long part somewhere out of the way, such as on a line of its own at the end of the message.

* If you need rich commit messages (links, lists, and so on), pick one markup language and stick with it. It'll be easier to write useful commit formatters if you only have to deal with one syntax, rather than four. Personally, I use Markdown when I can, or a reduced subset of Markdown, as it's something most developers I interact with will be at least passing familiar with.
