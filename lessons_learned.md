<h1>Intro</h1>
<p>This article is a summary of the most important lessons learned over the first few years of my career. It won't contain much (if any) code, but instead will talk generally about guidelines that can be considered before writing any code.</p>
</br>

<h1>Lesson 1: Read the Documentation</h1>
<p>It's tempting to skim over documentation to get an overview of how a particular library, framework, or component functions and begin to implement a feature right away. The problem with doing this is that the devil is in the details when it comes to development and it's likely your use of the third party functionality conflicts with how it was designed to be used, causing inconsistencies and ultimately hard to maintain code. Some specific issues include:</p>
<ul>
  <li>
    <b>Your use may be inconsistent with the original design</b>
    <p>The typical issue here is that a feature is implemented using a library but some alternative to the default behavior of the library is required. If you're not aware of what functionality the library provides the temptation is to override the library's behavior in some way which usually results in a workaround which is hard to maintain and, potentially, multiple workarounds which are exponentially harder to maintain.</p>
    <p>The effect is instead of code that flows simply from beginning to end like A -> B -> C, where B is the functionality we should have used, we have a flow like A -> D -> F -> E -> C, where D, E, and F are our workarounds. Making the situation worse in my experience is that these workarounds tend to refer to each other cyclically, where D, E, and F may refer to each other and change properties of each other, resulting in even harder to maintain code.</p>
  </li>
  <li>
    <b>Your may wind up "programming through permutation"</b>
    <p>This happens when you're implementing a feature and have sub-tasks A, B, C, and D. Task A gets implemented with minimal reading of the documentation, task B requires a little more reading of the documentation and some small changes to A, implementing C requires more reading and large changes to A and B, and so on.</p>
  </li>
  <li>
    <b>You may reimplement functionality that already exists (reinventing the wheel)</b>
    <p>If some third party has published a library or framework it's typically the case that related functionality will be provided (e.g., a math library will contain constants, types, and operations on those types). The developer of that library should provide ongoing support and bug fixes for it (and if they don't, tread carefully), so they have already made the commitment to updating and maintaining it, saving you the trouble.</p>
    <p>Reimplementing functionality means that you are now responsible for the time spent developing, maintaining, and fixing issues with the reimplemented functionality. In short, life's hard enough. Why make it harder?</p>
  </li>
</ul>  
</br>

<h1>Lesson 2: Be Aware of the Two Methods to Implement a Feature</h1>
<p>This point isn't about code quality (but does affect it), but instead is about the approach used to implement a feature.</p>
<h2>Method 1: Rush</h2>
<p>This method involves taking the requirements for a feature and immediately going to work on implementing them without having a complete picture, either because the requirements or your understanding of them is incomplete, or your understanding of how the feature will need to be implemented is incomplete from a technical perspective. There are three outcomes to this method:</p>
<ul>
  <li>
    <b>Failure</b>
    <p>A common outcome. The feature wasn't implemented on time, or is only partially working.</p>
  </li>
  <li>
    <b>The implementation is complete, but buggy.</b>
    <p>In this outcome, the implementation is complete and meets the requirements but the code is a complete mess. It suffers from poor abstractions, tight coupling, unnecessary dependencies, and the flow is difficult to follow. This outcome essentially kicks the cost of development down the road, where maintaining it will cost more than it should.</p>
  </li>
  <li>
    <b>The implementation is complete</b>
    <p>Through sheer dumb luck. Doesn't happen much.</p>
  </li>
</ul>
<p>The theme of this method is 'poor': poor understanding, poor implementation, poor outcome.</p>
<h2>Method 2: Deliberate</h2>
<p>The second method involves careful understanding of the requirements and formulating a clear plan on how to satisfy those requirements through your implementation. The steps typically look like:</p>
<ol>
  <li>
    <b>Gather requirements</b>
    <p>This involves gathering as much information as you can from stakeholders and getting a clear picture of what needs to be done. This step is crucial, so keep going into the details until you've reached the level of detail where that level no longer provides any more benefit.</p>
  </li>
  <li>
    <b>Consider the implementation</b>
    <p>Think about how the requirements can be satisfied by the implementation. What behaviors need to be modeled? What abstractions represent the desired functionality effectively? What types need to be defined? What operations need to be defined? What algorithms will I use or need to define? What data structures will I use or need to define? What existing third party code can I leverage in the implementation?</p>
    <p>Software design is outside the scope of this article, but this is where good design comes into play.</p>
  </li>
  <li>
    <b>Prototype</b>
    <p>Mock up a simple version of the implementation. This helps to define details of the requirements and implementation that may have been missed, as well as expose deficiencies in the implementation as they relate to software design.</p>
  </li>
  <li>
    <b>Repeat Steps 1-3 as required</b>
    <p>The implementation is rarely right the first time.</p>
    <p>The more times step 3 is repeated, the closer it goes from 'simple version' to a complete implementation.</p>
  </li>
  <li>
    <b>Implement and submit</b>
    <p>Proceed with the implementation.</p>
  </li>
</ol>
<p>The outcomes to this method are:</p>
<ul>
  <li>
    <b>Failure.</b>
    <p>Even after all that due diligence sometimes the implementation still results in failure. There may have been a requirement that wasn't satisfied because it wasn't brought up, it wasn't technically feasible, we couldn't find a way to implement it, or the implementation has gone on longer than scheduled.</p>
  </li>
  <li>
    <b>Success.</b>
    <p>The implementation satisfies the requirements and is easy (or relatively easy) to read, reason about, and maintain.</p>
  </li>
</ul>
<h2></h2>
<p>The difference in these two methods goes beyond whether the implementation was a success or failure but also determines how much you got out of the experience. When rushing, you typically develop only a superficial understanding of the topics you come across, where in the deliberate method time is taken to understand new concepts. This is effectively the same as the first lesson where gathering knowledge ahead of time overwhelmingly works to your benefit, even when it seems like that would make the task take longer. In being deliberate - regardless of the outcome - the knowledge you gain will stick with you: and, if you're going to fail you may as well be a little more knowledgeable after.</p>
<p>It's tempting to dive into an implementation to try and produce something right away - especially when you're getting paid to produce results - but it's rarely the right way to approach an implementation. Constantly rushing results in the same mistakes being repeated over and over which stalls your progression as a developer.</p>
</br>

<h1>Lesson 3: Prototype</h1>
<p>Lesson 2 already brought up prototyping but it's so important it needs repeating. A simple heuristic is: the version of the implementation you start with should not be the version you end up with. It's almost impossible to get any non-trivial implementation right the first time, so treat the initial implementation as a way to gauge what the final implementation may look like.</p>
<p>Remember that having an implementation 'working' isn't the same thing as correct or maintainable, so treat the initial version as something to be improved upon.</p>
</br>

<h1>Lesson 4: Cultivate a 'Tourist' Mindset</h1>
<p>A tourist travels from place to place, seeing the sights and collecting souvenirs. A good developer travels from topic to topic collecting ideas. This is in contrast to a worker taking transit to go directly from home to work, and a developer that is only exposed to concepts that are relevant to their current task.</p>
<p>Without exception, improvement needs to be constant and ongoing as a software developer and a tourist mindset makes this process natural. My suggestions for enacting this mindset include:</p>
<ul>
  <li>
    <b>Stay active in developer communities</b>
    <p>Social media, Q&A sites, and websites of other developers all contain a wealth of (free!) information and discussion, so take advantage of it.</p>
  </li>
  <li>
    <b>Read more code than write</b>
    <p>You'll be exposed to all kinds of ideas you otherwise wouldn't have come across but also get a chance to see how other developers (some with decades of experience) approach a problem.</p>
  </li>
</ul>
<p>In considering what makes the best developers I know successful, a tourist mindset is the commonality. Ideas from many fields seem to converge towards software development, and you never know how some observed behavior in another field will model some behavior in a problem you're trying to solve. Similarly, you never know when an algorithm or software design principle may become relevant. It's for your benefit to always be traveling from idea to idea.</p>
</br>

<h1>Lesson 5: Read More Code than Write</h1>
<p>I repeat this lesson because it's so important, and also to expand on it.</p>
<p>Part of reading others' code is being able to build and run it, and it's invaluable to be able take some code of interest off of Github (or elsewhere) and run it locally. This allows you to step through the code using a debugger to get a sense of how it works in practice, as well as modify the code to see how it responds. This is only possible by knowing how programs are compiled/built and ultimately run using your chosen technology.</p>
<p>When you first start learning software development setting up projects seems to include a million steps unrelated to coding, so it's understandable if these steps are only done as needed. But, past the basics it's well worth knowing the hows and whys of building and running projects.</p>
</br>

<h1>Lesson 6: Communicate Effectively</h1>
<p>Communicating effectively goes beyond language fluency and means that you can describe technical problems unambiguously; sometimes to a non-technical person.</p>
<p>Communicating a technical issue (in an increasing effective way) may look like:</p>
<ul>
  <li>"It doesn't work."</li>
  <li>"The button doesn't work."</li>
  <li>"The button doesn't trigger a signal when pressed."</li>
  <li>"The button doesn't trigger a signal when the enter key is pressed."</li>
  <li>"The button doesn't trigger a signal when the enter key is pressed because we aren't handling the enter key the same as we are the return key."</li>
</ul>
<p>A simple example for sure, but you can see that the issue being communicated becomes decreasingly ambiguous as more detail is added. As a bonus, if you can articulate and communicate a problem properly you can usually find a solution, as we did here.</p>
<p>Presenting a problem to a senior developer or manager is something you'll need to do from time to time, and someone who can report a problem unambiguously will certainly be favored over someone who can't. Someone who can report a problem <b>and</b> suggest a solution is favored 10 times more.</p>
</br>

<h1>Lesson 7: Integrate Changes in a Consistent Manner</h1>
<p>Imagine you were assigned a task, completed it, then submitted it for review. The review comes back with a small number of changes that don't require much further work, except that one that exposes a fundamental problem in your implementation. "No worries," you think, "I'll just add a small workaround and resubmit."</p>
<p>This works great until someone requests a change in your implementation and you need to take your workaround into account. Repeat for another change, and another, an so on until it's impossible to maintain without having to fix a mountain of bugs for every change introduced.</p>
<p>The takeaway is to treat submission of your work (both code and program) for review as just another step in the implementation. It's nice to leave a difficult task behind you, but if you view submission of your work as the final step then you won't address change requests as diligently as the original implementation. Sometimes, the original submission may actually be the first of a few implementations that you will go through until you reach the final implementation.</p>
</br>

<h1>Lesson 8: Good Software Design is Simple and Consistent</h1>
<p>I was tempted to leave this out because it's so hard to define - it's more of an art than a science - but, it's overwhelmingly the sign of an experienced developer to write easy to read and easy to maintain code.</p>
<p>Caveat: 'simple' and 'easy' are relative: some code just won't make sense because the domain of the problem isn't familiar to you (e.g., a rendering engine won't make sense if you don't know anything about graphics). It may be possible to learn about a domain through code, but it's usually easier to become knowledgeable about a domain then look at the code.</p>
<p>As developers we can go on forever about what determines good design, but from my own experience the following points are where I saw the most improvement:</p>
<ul>
  <li>
    <b>Easy to read.</b>
    <p>The code makes sense for the given domain and contains minimal surprises.</p>
  </li>
  <li>
    <b>Edge cases are minimal.</b>
    <p>Edge cases are handled as regular cases because the code around it is structured to turn those edge cases into regular cases.</p>
  </li>
  <li>
    <b>No workarounds.</b>
    <p>The code doesn't need workarounds become the logic is consistent. Workarounds are sometimes required when working with third party code, but if you're working on new functionality then workarounds are a sign that it's poorly designed.</p>
  </li>
  <li>
    <b>State can be inferred.</b>
    <p>Components of the program do not need to store extra variables to track the state of themselves: the state can be derived from the component itself. A simple example of this may be a stack implementation that can be checked if it's empty. Instead of storing a boolean to track if it's empty the implementation can just return true if its element count is 0.</p>
    <p>The problem with extra variables is that they all need to be updated correctly and might have dependencies between each other which makes updating them even harder. An exercise would be to try and remove a variable arbitrarily and see how the component would need to change to support the removal of that variable. Another option is to break those variables out into separate classes or functions that can take responsibility for managing and/or updating one, a few, or all of them in a cleaner way. This may also involve breaking up the component into smaller pieces that can combine back into the original component.</p>
  </li>
</ul>
<p>Seeing is believing when it comes to good design, but do keep this guideline in mind and over time you will come to appreciate just how beautiful well-written code can look.</p>
</br>

<h1>Lesson 9: Understand Modeling</h1>
<p>I've used the term 'model' and 'domain' a few times throughout this article but haven't yet defined what it means. You may hear about it in the context of the Model-View-Controller (MVC) pattern where the model is separated from the view.</p>
<p>Defining some of these terms simply in my own words:</p>
<ul>
  <li>
    <b>Domain</b>
    <p>Represents the main 'topic' that a program or component references. Some examples for clarity:</p>
    <ul>
      <li>Accounting software makes use of the domain of accounting.</li>
      <li>Router firmware makes use of the domain of networking.</li>
      <li>
        <p>Video games make use of the domains of graphics, physics, audio, etc.</p>
      </li>
    </ul>
  </li>
  <li>
    <b>Model</b>
    <p>Defines the logic of a given domain in code.</p>
    <p>The logic may follow the domain closely (e.g., accounting software), or may present simplified logic for easier implementation and/or more efficient computation (e.g., game physics).
  </li>
  <li>
    <b>View</b>
    <p>Represents the visible part of the program. Could be the visible part of a website, a desktop program, or the on-screen graphics in a video game.</p>
  </li>
  <li>
    <b>Controller</b>
    <p>Acts as an intermediary between the model and view: converts actions from the view (e.g., a button press) into operations on the model (e.g., perform some computation using the model's logic), and returns data from the model to the view (and may format or filter that data).</p>
  </li>
</ul>
<p>Modeling is important because it allows you to think about the logic and data of the program independently of how it will be used. The idea is that whatever needs to present the data created by or contained in the model doesn't need to know about the internals of how the model operates. For example, the renderer in a video game doesn't care about how the physics model calculated how fast an object is moving, the renderer only cares about where that object is relative to the camera and other objects.</p>
<p>Whenever I'm working on a model I imagine that its data will never be output, meaning that a program can run the logic of the model without ever showing it on a screen. This allows me to think solely about the function of the model without having the view influence the model: the logic of the domain (model) will remain static even if the way its presented (view) changes.</p>
</br>

<h1>Lesson 10: Document All Attempts at Solving a Problem</h1>
<p>I can't recall the number of times I've had to solve a problem and endlessly went over possible solutions and sometimes having to backtrack over previous solutions: trying to see if a small change would have made a difference.</p>
<p>The process looks something like:</p>
<ul>
  <li>Make a first attempt at solving the problem.</li>
  <li>It doesn't work, so some modifications are made that also don't work.</li>
  <li>A second attempt is made at solving the problem using a new method.</li>
  <li>It also doesn't work, so some modifications are made that also don't work.</li>
  <li>This process is repeated a number of times.</li>
  <li>Some insight leads to a previous attempt appearing to provide a solution to the problem, with a modification.</li>
  <li>It also doesn't work.</li>
  <li>This process is also repeated a number of times.</li>
</ul>
<p>The lesson is that nothing was gained from the previous attempts at solving the problem. No insight was gained about the problem itself: all that is left is a pile of candidate solutions that don't fix the problem. Previous attempts weren't thoroughly analyzed and are partially forgotten when they're returned to, so a lot of the things learned when they were first attempted have to be relearned.</p>
<p>By documenting and analyzing attempts at solving problems we can also gain insights into why the solutions didn't work. The candidate solutions that don't work provide hints to where the 'boundaries' of our failed solutions are, and those boundaries can provide starting points to dig deeper into what we're trying to fix and the inherent properties of that system or model that exist independently of our code.</p>
<p>Sometimes when we're fixing problems we don't need to fix the problem itself, instead we need to understand how our implementation failed to properly describe the desired behavior of the program in code. The apparent issue is just a symptom of our poor description of the behavior. To this end, it's important to be aware of the distinction between minor issues - like forgetting to assign a value to a variable - and structural issues, which may require us to examine our understanding of the system or model we're trying to create. It may be that once our understanding has improved it becomes obvious that the issue was actually the implementation of the system or model itself and it needs to be fully or partially re-written.</p>
<p>I can say my most miserable experiences as a developer were a result of spending weeks on trying to fix some particular issue or implement something and going over many possible solutions until one was found. Unfortunately, problem solving isn't an exact science, but hopefully the picture I paint here inspires you to be diligent in your problem solving and to not completely discard solutions that don't appear to work without thoroughly analyzing them.</p>
</br>

<h1>Miscellaneous Lessons</h1>
<ul>
  <li>
    <b>Keep commits small.</b>
    <ul>
      <li>Easier to review and merge.</li>
      <li>Easier to find where a specific issue was created because it's not mixed with other changes.</li>
      <li>Easier to revert a specific change because it's the sole change in a commit.</li>
    </ul>
  </li>
</ul>
</br>

<h1>In Closing</h1>
<p>I've found that the biggest progress I've made isn't from what I've learned about the specifics of software development but progress instead comes from improving the approach I use when learning and developing software. Even if these lessons seem obvious - or if they don't seem relevant right now - if they aren't acknowledged you will inevitably wind up making these mistakes repeatedly until you figure them out. Since I've already made these mistakes, why delay your progress any longer by repeating them?</p>
</br>
