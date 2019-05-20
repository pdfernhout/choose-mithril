## Why I prefer Mithril over Angular and React

*tl;dr*: Choose [Mithril](https://mithril.js.org/) whenever you can for JavaScript UI development because Mithril is overall easier to use, understand, debug, refactor, and maintain than most other JavaScript-based UI systems. That ease of use is due to Mithril's design emphasis on appropriate simplicity – including by leveraging the power of JavaScript to define UIs instead of using an adhoc templating system. Mithril helps you focus on the essential complexity of UI development instead of making you struggle with the accidental complexity introduced by problematically-designed tools. Many popular tools emphasize ease-of-use through looking familiar in a few narrow situations instead of emphasizing overall end-to-end simplicity which -- after a short learning curve for Mithril -- leads to greater overall ease-of-use in most situations.

### Introduction

Mithril, Angular, and React (as an ecosystem) are all software systems that support different ways of writing User Interfaces (UIs) in JavaScript for single-page applications that run in web browsers. The choice you make of which to use has different implications about how you write you code, how you debug it, and the end-user experience based on how you spend your time (i.e. whether you spend your time on essential complexity of the UI task instead of spending time on [accidental complexity](https://en.wikipedia.org/wiki/Accidental_complexity) introduced by your tool choice). Each tool also has implications related to developer training and availability of third-party components.

While there are many differences, most fall into roughly these categories for Angular, React, and Mithril:

Category | Angular | React | Mithril
--- | --- | --- | ---
Defining UI Elements | Templates | JSX | HyperScript API
Typical styling | Virtualized by component | Semantic Tooling | Functional/Atomic
Redraw approach | Dirty Checking | State Changes | User Action
Data model | Component & Services | Component or Redux | Flexible
Complexity of debugging | Very High | Medium to High | Low
Size of library and learning curve | Large | Medium | Small
Availability of third-party components | Medium | Large | Small

I will explain these in more detail in the following sections. Because JavaScript is so flexible, what follows is generalizations based on community practices and norms. You can of course do anything with any of these libraries. But in practice you won't because of different development cultures which put pressure on you to conform to group norms especially within a corporate setting.

Also, while I discuss only three UI systems here, much of what I have to say about Angular and React also applies to other popular UI systems like Vue and Aurelia. Likewise, what I say about Mithril applies to some other vdom libraries that emphasize the HyperScript API like Maquette – as well as to a lesser extent about React itself if you use it with the HyperScript API instead of JSX and also make some other departures from common practices when using React.

As a caveat, I have years of experience with Angular and Mithril, but only months of experience with React. So, people with years of experience with React and also some experience with Mithril should feel free to provide corrections or clarifications based on their own comparative experiences.

### Defining UI Elements: Templates vs. JSX vs. HyperScript API

Angular defines UI elements using a non-standard somewhat-HTML-like templating language. There are special constructs in the templating language you need to learn for loops, conditional display, and setting up one-way or two-way data binding to data stored in a component or elsewhere. These templates are almost always stored in separate files from the logic of the component. JavaScript in these templates is essentially just strings. As a consequence of embedding strings of code into templates, there is usually no way to know if the JavaScript has typos or such until you run the code -- and further, when you refactor your code, it is easy to miss that you need to change code strings in template, leading to more runtime errors.

React apps also define UI elements using a nonstandard somewhat-HTML-like templating language invented called JSX. JSX templates are usually in the same file as the logic of the component. Facebook, the creator of React, was a long-time PHP shop, and JSX essentially makes JavaScript look a lot like PHP. While this may seem to make UI development easier, in practice, you need to set up special build steps to transform the JSX into calls to React.createElement(). Also, you need to mentally context switch from JavaScript to JSX to JavaScript to JSX and so on multiple times to use JSX -- where these mental context switches makes JSX harder to write and read than plain JavaScript. JSX is also harder to refactor than plain JavaScript because of all these context switches.

Mithril apps usually define UI elements using the "HyperScript" API. For people who know React, the HyperScript API is almost equivalent to adding "const h = React.createElement()" at the top of your file and then using h("elementName", ...) calls in your code to define the UI -- where the h(...) calls ultimately result in assembling or modifying a tree of HTML elements in the browser the same way JSX does that. Mithril actually uses "m(...)" instead of the more common "h(...)" for HyperScript API calls -- the m standing for Mithril. The main advantage of the HyperScript API over using HTML-ish templates or JSX is that you just write your UIs in plain JavaScript. There learning curve of the HyperScript API is very low for any JavaScript developer -- and then a developer can leverage all their JavaScript knowledge to define, debug, and refactor UIs. There is also no need for an additional build step making it easier to get started with development -- unless you need a buil step for other reasons like to use TypeScript or Saas/SCSS. It is true that the HyperScript API may look unfamiliar to developers used to coding UIs using HTML-ish templates, but once developer learn the new approach (in a matter of hours), they can discard as obsolete all the non-standard templating languages they learned. Multiple UI libraries support HyperScript (using with "h" instead of "m" though), so that new knowledge can be leveraged in lots of places. As a bonus, the HyperScript API supports setting CSS classes by adding them after the element name, so "div.class1.class2#id3" defined an HTML div with two classes on it and an id. For all these reasons, HyperScript code tends to be both more concise then HTML templates or JSX -- while also being more readable once someone becomes familiar with it.

As an [example modified from the Mithril.js.org website](https://mithril.js.org/jsx.html):

        // The HyperScript API calls in plain Mithril:
        
        var MyComponent = {
          view: function() {
            return m("main", [
              m("h1", "Hello world"),
              m("ul", ["a", "b", c"].map(letter => m("li", letter))
            ])
          }
        }

        // can be re-written in JSX as:
        
        var MyComponent = {
          view: function() {
            return (
              <main>
                <h1>Hello world</h1>
                <ul> { ["a", "b", c"].map(letter => <li>{letter}</li>) } </ul>
              </main>
            )
          }
        }

The reason development team after development team makes the mistake of choosing templates or JSX over using the HyperScript API is that templates and JSX kind-of look familiar because they look similar to HMTL at first glance. What is not so obvious at first glance is that templates and JSX are not standard HML and they both have various gotchas. Both templates and JSX rapidly become harder to read as the UI code gets more complex like with loops and conditionals involving switching back and forth repeatedly between HTML and JavaScript. HyperScript is much simpler since it is all just JavaScript -- but HyperScript it not immediately familiar to someone who has spent years defining UIs using HTML-ish templates. Rich Hickey in ["Simple made Easy"](https://www.infoq.com/presentations/Simple-Made-Easy) goes into detail on the negative consequences of people mistaking familiarity for simplicity. Essentially, simple * simple * simple can multiply together to simple (where simple is often -- but not always -- easy to use), whereas easy * easy * easy can multiply together to complicated and difficult to deal with overall.

### Typical styling: Virtualized by component vs. Semantic Tooling vs. Functional/Atomic

CSS stands for Cascading Style Sheets. CSS is a domain specific language about styling HTML elements. In practice, the well-meant "cascading" part of stylesheets in large applications eventually leads to brittle styling where developers are afraid to change or delete anything because they don;t have time to test all the implication, and so stylesheets tend to grow over time and become big confusing messes. There are three ways prevent that mess as localized styling by component or. semantic tooling or Functional/Atomic CSS. The second two approaches can be used with any of UI library. But, in practice, developers of Angular and React tend to mainly use one approach as a "norm" for the developer culture.

Angular developers tend to use styling localized by component, because that is what the Angular docs promote and Angular goes to a lot of trouble to make that possible. Essentially, Angular components encapsulate component-specific stylesheets. These stylesheets tend to be written in one separate file per component -- along with usually some global styling in another file (or files) that applies to the entire application. The Angular approach here helps isolate against the "cascading" problem of stylesheets. It is not a bad solution to CSS complexity by itself -- but it is Angular-specific (even as the CSS itself is standard).. And also the having templates, styles, and code in three separate files (in most cases) makes coding UIs in Angular an exercise in bouncing between files in the IDE a lot -- where each context switch between files can disrupt a train of though, making development harder than it has to be.

React developers tend to (eventually) move to using tooling for semantic markup like Saas/SCSS. Saas stands for Syntactically Awesome Style Sheets". Semantic styling is about styling by use case, like an element with a ".comment" CSS class. Sass has four syntax parsers: SCSS, Saas, CSS, and Less, where all of these convert a different syntax into eventually into CSS output. Saas is a more concise way of writing CSS. SCSS is more an extension of CSS. Something like Saas/SCSS defines a non-standard extension to CSS to add more general programming capabilities. SO, for example, a constant can be defined which is reused in multiple styles. Or there can be calculations related to the constant or various conditions. SCSS is great for managing the complexity of the "cascade" -- but it is one more thign to learn, one more set of tooling to maintain, requires a build step, and is non-standard. There are other ways of writing more modular CSS that some React developers use which are more like what Angular supports, but instead of Angular automatically isolating CSS by component, developers add component-specific prefixes to style names (e.g. [BEM](http://getbem.com/naming/) -- Block, Element, Modifier). React also promotes using inline styles as well -- which can work well in practice from a developer perspective but can also impact performance somewhat.

Mithril developers often use Saas or BEM-like-approaches too for semantic styling -- but the current trend in the Mithril community is more towards something called ["Atomic" or "Functional" CSS](https://css-tricks.com/lets-define-exactly-atomic-css/). Angular or React developer could of course use Atomic/Functional CSS too, but they tend not to because of current cultural norms. Atomic/Functional CSS uses class names that say what they *do* not what semantic type of displayed item they are intended to affect. So, rather than tag a displayed div with a class like ".comment", atomic/functional CSS would be more likely to use (with the Tachyons CSS approach) ".blue.h2.fw2.mw3" to set the color, height, font weight, and maximum width. This will seem odd -- even immoral -- at first to anyone used to semantic CSS. And probably it is bad if not impossible to use it when trying – after the fact -- to style HTML generated in big chunks by someone else (CSS’s original use case). But, in the context of writing components for single-page applications, Atomic/Functional CSS tends to work really well. This is because CSS often is specific to components and so you have to put it somewhere -- so the only two obvious choices are in an external stylesheet about a class associated with the component or alternatively directly in the component itself. Putting the styling in the component makes it easier to find. The component code itself acts as the semantic unit – and “duplication” of styling is avoided by having that style in just one place in your code – in the component code itself. Also, because Atomic/Functional CSS uses real styles and not inline styling, Atomic/Functional CSS is quicker to render by browsers for technical reasons than inline styling. Finally, it is much easier to reason about styles which are named by what they do -- not by the semantic meaning of the element the styling is intended to modify. It is also sometimes hard to come up with semantic names for all the individual parts of a web page, and so that step becomes optional when using Atomic/Functional CSS. For applications that want to be theme-able, or that want to consolidate design choices about colors or font sizes, because you are just coding in JavaScript with Mithril, you can just do string manipulation on elements (like "div" + themeColor() + currentSize() ). That provides all the power of using Saas, but without having to learn another specialized language or require a build step. The Atomic/Functional styling approach I prefer is called Tachyons, but there are other approaches out there, and ultimately Atomic/Functional CSS styling is more a design pattern than a specific implementation. Does Atomic/Functional styling have limits? Yes. But in practice, using Tachyons plus an occasional inline style and maybe a short general application-specific stylesheet means you very rarely need to modify a stylesheet when working on a component. This makes initial UI coding go faster and also makes debugging styling issues go faster too. If you use Atomic/Functional CSS like with Tachyons, and you use HyperScript, you can, for the most part, spend all your time programming in JavaScript without ever touching an HTML template or a CSS stylesheet – providing a substantial developer performance boost by avoiding switching mental contexts between languages or between files.

### Redraw approach: Dirty Checking vs. State Changes vs. User Action

Smalltalk was the first major UI development system made in the 1970s -- and it inspired Steve Jobs and the first Mac UI. Smalltalk pioneered the notion of custom-written models that sent "changed" messages to dependents when their state changed. Those dependents were typically UI elements who would then redraw themselves when they got the "changed" message. A lot of code need to be written to hookup the models to the UI elements. And then code needed to be written so when the UI elements got clicked ot typed on they would update the models -- as a "two way" dataflow. A common "two way" failure mode unfortunately was "ringing" in the UI, where UI interactions would change models which would update UI elements which would cause more changes to the models and so on. This could be usually worked around – but it lead to brittle and hard to debug code sometimes (often involving adhoc flags being set to know whether changes were being made by the user or the application). When you got it all right, it was very computationally efficient -- a big issue back then. But when you got it wrong, the UI could unexpectedly lock up, or it could get laggy with lots of extra redraws of components, or what was displayed might be (unobviously) out-of-date. Even now, such an approach to writing code is more power-efficient on mobile devices. That approach to writing UIs spread to all sorts of libraries, C++'s wxWidgets, Java's Swing, and a variety of JavaScript libraries (e.g. Backbone/jQuery and Dojo/Dijit/Dmodel).

But nowadays, personal computers are literally millions of times faster and more power-efficient than in the 1970s and 1980s. So, we can consider other approaches which trade off some extra computation to gain reduced development time and easier maintenance. In general, the newer UI approaches work more like writing video games -- where UIs are redrawn in whole or part when the UI system detects that some part of the application's state has changes somehow. This "one way" data flow makes code much easier to reason about. In theory, all you have to consider is how the application should redraw itself based on current application state -- as well as how a user interaction or network request or timeout or similar should change the current application state. Still, it remains wasteful and sometime laggy to rerender a UI in the browser every frame 60 times a second – especially on mobile devices -- so as an optimization, applications still ideally need to only redraw themselves when they have to.

Angular uses "dirty checking" to know when to redraw. To support that, Angular uses a library called ["Zones"](https://blog.bitsrc.io/how-angular-uses-ngzone-zone-js-for-dirty-checking-faa12f98cd49). Whenever Angular detects that application state has changed, it redraws only the components which depend on that application state. Zones are especially good at tracking when network requests finish and change application state and then causing a redraw.  This approach usually works pretty well -- except when it doesn't (like when application code throws an exception). Then, suddenly, the application developer needs to debug through hundreds of stack frames related to behind-the-scenes Zones machinery and other Angular machinery. That sort of debugging can be fairly frustrating -- as what may be a very simple error to fix becomes hours of struggle to understand – because of all the extra "helpful" Angular machinery in the way. Developers may also have to struggle to understand where Angular "expression has changed after it was checked" error messages are coming from a subtle interplay of component initializations or other changes. In that case, Angular in debug mode detects that application state was changed in the process of rendering. Usually -- but not always -- that error is from something innocent like lazy-loading of a value causign a change, say, from an undefined to a null or an empty string. Zones also have another implication for standard Angular functional testing using Angular's Protractor "to measure angles" tool -- where Protractor can quickly go through many UI changes it does not have to wait for given its understanding of Angular Zones -- unless you do network requests or other background processing including timeouts which then essentially breaks Protractor until you rewrite that code to run in another Zone so Protractor can handle it better. While that is all doable, it ultimately in practice requires developers to develop a deep understanding of some complex behind-the-scenes code if they want their Angular application to be debugged and tested. Alternatively, you might be tempted to turn off the special feature that makes Protractor fly through tests and start adding manual timeouts to tests which introduces a whole different set of issues. Dirty checking also can slow down an application with complex UIs which draw from hundreds of global values -- and such UI slowdowns at scale were common with AngularJS (a predecessor to Angular 2/4/5/etc.). 

But, as if all this complexity was not enough, Angular promotes the use of RxJS, which is a essentially a library for constructing pipeline networks of observables that emit changes. (An alternative would have been to promote async/await and a common cancellable promise.) Essentially, RxJS is a concise way of writing code that is difficult to understand or debug. Beyond that, the documentation for RxJS has long been problematical to navigate – and other aspects of it it have been inconsistent (e.g. if you import a class with a certain name from a different location in the library in earlier versions things may behave differently). The multiplicative complexity of combining RxJS with Zones can make debugging and maintaining an Angular app can become a difficult task for many developers. On the plus side, RxJS is a de-facto standard API with multiple implementations that can be used in Java and other languages, and so arguably may be worth learning as another tool for your toolkit for some special cases. In defense of RxJS, it appeared before async/await was commonly supported in JavaScript and so RxJS was more valuable before using async/await became common as a way to make complex asynchronous code appear synchronous and so be more easily read and debugged.

React uses "setState(...)" to know when to redraw. Essentially, every React component is encouraged (in the beginner books) to have a state. When a user interacts with a UI element, or a network request completes, or a timer fires, the code responding to those events is supposed to call component.setState(...) to tell a component and all its subcomponents to redraw. This call to setState(...) will optimally rerender only the part of a visible tree of components. But over time, React developers have discovered a few things though. One is that you can have components without internal state (usually called functional components) where any -- possibly varying -- configuration is passed in from other components via "props". Although sometimes it if discovered that such functional components could benefit from state, so a second way of defining state with a "useState(..)" function call was added in recent versions of Angular. This of course leads to confusion and disagreements about how to define React components and how to manage state. But more than that, insisting state will be held in a comportment was essentially a premature minimization as a design constraint. React's default design approach using setState() also encourages what Mithril's author, Leo Horie, termed (in another context) "fat controllers" where UI components end up holding onto a lot of application state and business logic.

To get around this design constraint and also take advantage of the possibilities vdom has to offer, React developers have (re-)invented a design pattern of one-way data flow called "Flux" or "Reflux" or now "Redux". The basic notion of Redux (as a library implementing the Flux pattern) is that you store as much as possible of your application state in a global store. A top level component tracks the store, and when the store is changed in any way, the entire UI is rerendered. Redux successfully defeats the premature optimization of having to use setState() in every component to force a redraw -- and overall Redux usually benefits more complex applications (even as you can also just do the same thing as a design pattern without the library). Not to be outdone in extra complexity by Angular, React has libraries like Redux-Saga – which is sort of like RxJS but with less generality and no support for other languages or UI libraries. Again though, in certain complicated situations, Redux-Saga can be useful -- although one might also question whether applications need to ever be designed in such a way that Redux-Saga might be useful. Of course, since Redux and Redux-Saga might be useful someday, as with RxJS, it is often promoted as a good practice to start using all that complexity from day one, thus increasing developer learning curve, making codebases overly-complex, and leading to difficult debugging also from day one. Much of that extra complexity from Redux and so on usually is all just to work around React's initial premature optimization of requiring a call to setState to rerender just a component and its subcomponents. There are more general approaches to the concerns that Redux and Redux-Saga try to solve though. One such solution is the more general pattern of a stack of "Command" objects (with do/undo/redo and tracking).Another solution is just implementing the Task pattern of an object that represents an ongoing background task which reports progress by updating a progress object and then making a callback or calling redraw. Code that uses such patterns is often cleaner when you then add the type of pattern to the end of the class of functions, like, say, "ChangeNameCommand" or "PollServerTask".

Mithril, in keeping with its emphasis on appropriate simplicity, uses a more elegant approach to know when to redraw the UI. Mithril just assumes that anytime the user touches the UI that the application state is now dirty and so the UI needs to be rerendered. Mithril does that by wrapping all definitions to "on..." methods like onclick, oninput, or onkeydown with a function that calls redraw after the event handler completes. In a few rare cases you may want to set a flag to prevent the redraw, like if you decide to ignore a keystroke. Mithril encourages developers to only optimize redraws using that flag when you see an actual issue in practice. Mithril also provides a way to request data over the network that also redraws when the network request completes. For other cases like setTimeout (which Angular handles with Zones or react with setState used in a setTimeout handler), you need to call redraw yourself. That is fairly easy to remember in practice, and generally obvious in testing when you forget. Or, you can wrap setTimeout yourself so it always calls redraw when it completes. In practice, having done a lot of Mithril development work, I find Mithril’s approach hits a sweet spot of minimizing debugging complexity (no Zones needed) while supporting design flexibility (no setState(...) calls needed) -- and so Mithril exceeds both Angular and React in both those ways. And the Mithril codebase can be much simpler for that insightful design choice -- where Mithril overall is only about 1000 lines of JavaScript, or about 10X less code than just the React UI library and 100X less code than Angular. And Mithril is faster too because it needs to do less behind the scenes work to render and support a UI.

### Data model: Component & Services vs. Component or Redux vs. Flexible

Angular promotes a model where state tends to either be stored in comportments or services. I actually like this model overall -- even if in practice a lot of state and business flapjack tends to end up in UI components (even when it is better in services). But that is more an issue of programmer discipline than a flaw of Angular – except to the extent Angular examples tend towards making complex components mixing application state and business logic.

React, as above, tends to promote state being either in a Component or in a Flux/Reflux/Redux global store. You tend to have to do mainly one or the other (even though there is some state like, say, current caret position in an editor that legitimately might live in a component, especially when you can have more than one at a time on the screen.

Mithril is flexible. One of Mithril's best features is that it has no opinion and imposes no requirements on how your app state should work. In a way, Mithril essentially has the Flux/Reflux/Redux model built-into how it works if you want it. And so, you can store state globally, in a single object store -- or in components (whether "fat" controllers or "thin" ones) -- and basically it all works either way. A Mithril app does require some thought sometimes to decide where the state should best live. But ultimately that choice is up to the developer or the team and you can refactor that decision over time as needs change. I tend to use as much "global" state as possible in Mithril apps -- where by global I mean just plain old variables shielded inside a function or class defining the application, with some values (often as objects) passed into subcomponents or accessed via specialized adapters which start from the application state. Other developers tend to use a lot of component state. Ultimately the best choice depends on the application and also what the developer is comfortable with. If the application is just one file's worth of code, then defining state within a class or function may make a lot of sense. If it is a big application, defining an application object and maybe some other similar top level objects which are all passed around may make more sense -- as might using more component state. And, in some situations, defining all state in a Redux-like store might instead make more sense -- or some mix of all of these.

### Complexity of debugging: Very High vs. Medium to High vs. Low

As above, because of Zones and RxJS, Angular tends to at first be very hard to debug when something goes wrong. Eventually, if you are unfortunate enough to have years of experience with Angular (like I do) then debugging is not quite so bad. 

React tends to be medium hard (sometimes even easy) to debug if you stick with using setState with components. Unfortunately, almost certainly someone will want to start off with Redux and Redux-Saga "just in case you might need it later", and so React debugging in practice can become highly difficult -- although probably never as difficult as when Angular goes wrong.

Mithril is usually a joy to debug. There are still some well-understood pitfalls for new users. Those include setting the value in an input but not having an oninput or onchange handler. They also include forgetting to call redraw in a setTimeout callback or after a fetch request completes. Mithril's error reporting admittedly is also as good as React's. Overall, I consider those a small price to pay for code that otherwise is straightforward to write, debug, refactor, and maintain.

### Size of library and learning curve: Large vs. Medium vs. Small

Angular is a beast of a framework. I have been using it for three years full-time and I still find edge cases, gotchas, and new features -- especially as version of version of Angular continues to roll out. There is a 100 page book alone you need to read just to use the recommended Angular router. Angular dependency injection is another complexity I have not gotten into here, which adds yet another layer of complexity for debugging code and also writing unit tests. In practice, in a real Angular applications, most Angular dependency injection complexity seems like could be replaced by just setting up a set of service instances of various types hanging off an application. That is a case where Angular adds an immense amount of complexity to support generalized dependency injection when in practice, for most apps, you probably aren't going to need that generality – but you pay the price for it everyday in learning curve and more complex code to understand, debug and test. If you plan to use Angular, expect to spend months before you begin to feel somewhat comfortable and productive. I am sure a few people reading this will chime in on how absolutely essential this feature of that feature of Angular is to their super-complicated enterprise app and they and 100s of developers in their company have mastered Angular and love it for the repeatability it brings to laying out enterprise apps – that can be true even while it can also be true that Angular creates needless complexity for most applications that use it.

React itself is not that hard to learn. The learning curve comes when you wander into the rest of the React ecosystem like picking a Router or picking a way to store state like Redux to get around React's component-oriented setState() premature optimization. So while React itself can be picked up in a day or so, expect to spend weeks or months going through all the rest. It's probably not hat bad a journey overall because Facebook's involvement has made React so popular there and many tutorials and so on. Of course, then you need to decide which of all those learning resources to use. An =d also, please don’t confuse being able to get quickly started with something like Reactstrap for true understanding – you may be able to quickly create an environment you can write React code in – but that does not mean you can understand or change most of what is involved in that setup without a lot more learning.  

Mithril's basics can be learned in a few hours -- for someone who already knows JavaScript, CSS, and HTML -- and shortly thereafter you can be defining UIs in HyperScript, routing pages, making components, and having a simple working app based on only about 8K of gzipped library code. You can also use JSX with Mithril -- but that seems like a big step backward to me and also requires a lot more tooling and library support. Basically, the main problem in using Mithril for most people is forgetting all the stuff you needed to learn to use jQuery or React or Angular and then rediscovering the joy of programming.

### Availability of third-party components: Medium vs. Large vs. Small

Angular has some third-party components. However, it is poised to leverage web components which may add to this (as they would for all other UI systems).

React has the most third-party components of all sorts. That is definitely a good thing about React.

Mithril has by far the least components of these three. If you want to do something in Mithril, you may well need to add it yourself. However, Mithril makes this relatively easy to do. You can also wrap components from, say, React or Dojo if you really want to. So, while less components is a negative about Mithril right now, I don't see it as all that big a deal. And the more people who use Mithril, the more FOSS components it will end up with. In practice, I like just using standard HTML components in many situations (e.g. text input fields, textareas, radio buttons, selects, buttons, etc.), which also tends to make UIs more accessible since screen readers and assistive technologies are good at dealing with basic HTML components compared to many fancier widgets. Also, because Mithril is so easy to work with when you use HyperScript and Atomic/Functional CSS, when you do want a custom component in Mithril, it tends to be easy to add one – sometime for simple cases even just via a simple function in the same code file that renders exactly what you want.

### Workarounds to make React and Angular more like Mithril

Despite Mithril being better in essentially every respect that matters to a developer compared to Angular and React, chances are you will be stuck using React anyway because Facebook has promoted it and it now has a lot of buzz, same as so many people feel they need a Facebook account to stay connected to the mainstream. React also has React Native for mobile development which may be compelling for some projects compared to a regular web app. If you are stuck using React, I'd suggest looking into [React-HyperScript](https://github.com/mlmorg/react-hyperscript) maintained by Matt Morgan from Uber to at least avoid the JSX mental context-switching overhead that will slow development by (guessing) a third or so. That is because 90% of programming time is reading code or refactoring existing code, not writing it the first time, and context switches slow reading and refactoring down. I'd also suggest using at Mithril-like pattern with React-HyperScript where you consider wrapping the "h" function to do a redraw of your top level component after every "on..." method completes. Alternatively you could just calling a redraw() function at the end of every such event handler where that forces a redraw of the top component. That way you can store you application state however you want in your application -- accepting that sometimes it is indeed best to store some state in components. I'd also suggest bucking the trend to SCSS or BEM by using React with Tachyons or a similar Atomic/Functional CSS library as much as possible. Essentially, the more you can make React look like Mithril, the better off you will be.

If you are stuck using Angular, you have my sympathy. About all I can suggest is adding Tachyons as a global stylesheet and beginning to use it and then selectively replacing UI pages and components one at a time with equivalent ones in Mithril (or React if that is all your company will accept, but React's design around setState() makes that harder) and then eventually at the end make one final big push to remove the rest of the Angular framework.

Of course, in a corporate setting you’d have to not just try those ideas yourself but also convince your team to try them – which is hard, and is probably much much harder than actually learning how to use Mithril.

### Conclusion

Please don't confuse popularity with fitness for purpose. The most popular (highest selling) motor vehicle in the USA is the Ford F-150 pickup truck. While F-150s are indeed great pickup trucks, if everyone drove only F-150s, highways would be more crowded, fuel consumption would go up, pedestrians and bicyclist would be less safe, there would probably be a lot more fatal accidents from limited visibility and lack of many safety features, everyone would have a lot harder time finding a place to park their vehicles, and also most people would have a harder time getting in and out of their vehicles. Use the right tool for the job. If Angular was a Semi-trailer truck, and React was like a F-150, Mithril by contrast might be, say, a Tesla Model S. No doubt there are some edge cases where Angular and React excel over Mithril, just like you'd rather haul freight in a Semi-trailer given huge hauling capacity and rather haul plywood in an F-150 given a big bed open to the elements -- while otherwise you'd probably prefer a Tesla Model S for hauling yourself, your family, and your groceries around for daily commuting given low maintenance and low per-mile costs. Overall, if you really look at the three different UI systems as above, I'd suggest use cases for Angular and React are not as common as one might expect compared to the benefits of Mithril as a daily driver with a much simpler all-electric drive train. The fact that Leo Horie's Mithril -- a project started by one brilliant, kind, and generous person and now supported by a few volunteers -- can attract as much interest and support as it does relative to Facebook's React and Google's Angular (both backed by millions of dollars of paid development and free publicity) implies a lot about Mithril's technical advantages and developer ergonomics.

YMMV (Your Mileage May Vary) of course. The above is just based on my limited personal experience -- and what I have learned from reading about the experiences of other software developers. Elm and ClojureScript are worth exploring too if you have a broader choice of languages. My thanks go to Leo Horie, Mithril's brilliant creator, and the small group of quirky individuals who as a team maintain Mithril as a labor-of-love and who continue to demonstrate its use in ever-more amazing ways.

A list of some of the companies and projects using Mithril can be found here:  
https://github.com/MithrilJS/mithril.js/wiki/Who-Uses-Mithril 

Good luck and have fun programming with whatever UI systems you end up using.
