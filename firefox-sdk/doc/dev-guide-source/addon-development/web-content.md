<!-- This Source Code Form is subject to the terms of the Mozilla Public
   - License, v. 2.0. If a copy of the MPL was not distributed with this
   - file, You can obtain one at http://mozilla.org/MPL/2.0/. -->

# Content Scripts #

Almost all interesting add-ons will need to interact with web content or the
browser's user interface. For example, they may need to access and modify the
content of web pages or be notified when the user clicks a link.

The SDK provides several core modules to support this:

**[panel](packages/addon-kit/docs/panel.html)**<br>
Create a dialog that can host web content.

**[page-worker](packages/addon-kit/docs/page-worker.html)**<br>
Retrieve a page and access its content, without displaying it to the user.

**[page-mod](packages/addon-kit/docs/page-mod.html)**<br>
Execute scripts in the context of selected web pages.

**[widget](packages/addon-kit/docs/widget.html)**<br>
Host an add-on's user interface, including web content.

**[context-menu](packages/addon-kit/docs/context-menu.html)**<br>
Add items to the browser's context menu.

Firefox is moving towards a model in which it uses separate
processes to display the UI, handle web content, and execute add-ons. The main
add-on code will run in the add-on process and will not have direct access to
any web content.

This means that an add-on which needs to interact with web content needs to be
structured in two parts:

* the main script runs in the add-on process
* any code that needs to interact with web content is loaded into the web
content process as a separate script. These separate scripts are called
_content scripts_.

A single add-on may use multiple content scripts, and content scripts loaded
into the same context can interact directly with each other as well as with
the web content itself. See the chapter on
<a href="dev-guide/addon-development/content-scripts/access.html">
content script access</a>.

The add-on script and content script can't directly access each other's state.
Instead, you can define your own events which each side can emit, and the
other side can register listeners to handle them. The interfaces are similar
to the event-handling interfaces described in the
[Working with Events](dev-guide/addon-development/events.html) guide.

The diagram below shows an overview of the main components and their
relationships. The gray fill represents code written by the add-on developer.

<img class="image-center" src="static-files/media/content-scripting-overview.png"
alt="Content script events">

This might sound complicated but it doesn't need to be. The following add-on
uses the [page-mod](packages/addon-kit/docs/page-mod.html) module to replace the
content of any web page in the `.co.uk` domain by executing a content script
in the context of that page:

    var pageMod = require("page-mod");

    pageMod.add(new pageMod.PageMod({
      include: ["*.co.uk"],
      contentScript: 'document.body.innerHTML = ' +
                     '"<h1>this page has been eaten</h1>";'
    }));

In this example the content script is supplied directly to the page mod via
the `contentScript` option in its constructor, and does not need to be
maintained as a separate file at all.

The next few chapters explain content scripts in detail:

* [Loading Content Scripts](dev-guide/addon-development/content-scripts/loading.html):
how to attach content scripts to web pages, and how to control the point at
which they are executed
* [Content Script Access](dev-guide/addon-development/content-scripts/access.html):
detail about the access content scripts get to the DOM, to other content scripts,
and to scripts loaded by the page itself
* [Communicating Using <code>port</code>](dev-guide/addon-development/content-scripts/using-port.html):
how to communicate between your add-on and its content scripts using the
<code>port</code> object
* [Communicating using <code>postMessage()</code>](dev-guide/addon-development/content-scripts/using-postmessage.html):
how to communicate between your add-on and its content scripts using the
<code>postMessage()</code> API
* [Example](dev-guide/addon-development/content-scripts/reddit-example.html):
a simple example add-on using content scripts
