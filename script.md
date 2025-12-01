# Presentation Script - Lecture to Notes Demo
## Natural, conversational script (10-12 minutes)

---

## SLIDE 1: Title Slide (30 seconds)

Hey everyone, I'm Amshu, and today I'm going to show you a project I built called Lecture to Notes. It's basically an app that takes your lecture recordings and automatically turns them into organized notes using AI.

So instead of frantically scribbling notes during class and missing half of what the professor says, you can just record the lecture and let the app handle the note-taking for you.

Let's dive in.

---

## SLIDE 2: The Problem (1 minute)

Alright, so first - why did I build this?

I'm sure you've all been in this situation: You're in a lecture, the professor is explaining something important, and you're trying to write it down. But while you're writing, you miss the next thing they say. So you're constantly playing catch-up, and by the end of class, your notes are incomplete and messy.

Or maybe you try to just listen and understand the material, but then when it's time to study, you realize you have nothing written down.

It's like you have to choose between actually learning the material or having good notes. And that sucks.

There's actually research showing that students only retain about 20-30% of lecture content when they're multitasking between listening and writing. That's terrible.

So I wanted to fix this problem - to let students actually focus on understanding the lecture while still getting comprehensive notes.

---

## SLIDE 3: My Solution (1 minute)

Here's what I built: a web app that handles the entire note-taking process automatically.

It works like this:
First, you record the lecture - either live in the browser or upload an audio file.

Then, the app sends that audio to Google's Speech-to-Text API, which is the same technology behind Google's voice typing. It converts the audio into a text transcript.

But here's the thing - a raw transcript is just a wall of text. It's not organized, there are no bullet points, no headers, nothing that makes it easy to study from.

So the third step is where OpenAI comes in. I send that transcript to GPT-3.5, and I tell it: "Hey, organize this into structured notes. Add headers, bullet points, highlight key concepts, make it readable."

And that's it. The student gets back both the full transcript and the AI-organized notes. They can focus entirely on understanding the lecture, and the app does all the documentation work.

---

## SLIDE 4: Why This Solution Makes Sense (1 minute)

You might be wondering - why not just build the AI myself? Or why not use an existing app?

Well, here's the thing: Google has spent billions of dollars and hired hundreds of PhD researchers to build their speech recognition. It's incredibly accurate - like 95% or better for clear audio. There's no way I could build something that good in a semester.

Same thing with OpenAI. Their language models are trained on basically the entire internet. They understand context, they can identify what's important and what's not.

So instead of trying to reinvent the wheel, I used these proven technologies and focused on making the student experience really good.

As for existing apps - yeah, there are services like Otter.ai, but they cost money. Like $10-$20 a month. I wanted something free for students, or at least cheap enough that the API costs are minimal.

Plus, this is a web app, so there's no download, no installation. You just open it in your browser and it works.

---

## SLIDE 5: How It Works - User Flow (45 seconds)

Let me walk you through what a student actually does.

They open the app, click "Start Recording," and just let their laptop or phone sit there during the lecture. The recording timer shows them how long they've been recording.

When the lecture ends, they click "Stop Recording." That's it from their end.

Then the app takes over. It uploads the audio, which takes about 5 seconds. Then it sends the audio to Google for transcription - that takes 20 to 40 seconds depending on how long the lecture was. Then it sends the transcript to OpenAI for organization - another 10 to 20 seconds.

So total processing time is usually 30 seconds to a minute. Not instant, but not bad for converting a 30-minute lecture into organized notes.

And at the end, the student gets both the full transcript and the structured notes with headers, bullet points, bold text - all formatted nicely.

---

## SLIDE 6: Tech Stack (1 minute)

Okay, let's talk about the actual tech.

On the frontend, I used React 19. It's the industry standard for building web UIs, and it's great for handling all the state management - like keeping track of whether we're recording, processing, showing results, all that stuff.

For styling, I used Tailwind CSS, which is basically a utility-based CSS framework that makes it really easy to build clean, responsive designs without writing a ton of custom CSS.

And for the recording itself, I used the MediaRecorder API, which is built right into modern browsers. So I didn't need to install any libraries - the browser can just record audio natively.

On the backend, I kept it simple. Node.js with Express. Express is super lightweight - it's basically just a way to create API endpoints. I didn't need a heavy framework like Django or Rails, just something to handle file uploads and route requests to the right APIs.

Multer handles the file uploads. And then I have the two main APIs: Google Speech-to-Text for transcription and OpenAI GPT-3.5-Turbo for structuring the notes.

The whole thing is pretty straightforward. No overengineering.

---

## SLIDE 7: Architecture Diagram (45 seconds)

Here's how all the pieces fit together.

The browser - that's the React app - handles the recording and displays the results to the user.

When the student stops recording, the browser sends the audio file to the Express server via an HTTP POST request.

The server then acts like a middleman. It takes the audio file and sends it to Google's Speech-to-Text API. Google sends back a transcript.

Then the server takes that transcript and sends it to OpenAI's API. OpenAI sends back the structured notes.

Finally, the server sends both the transcript and the notes back to the browser, and React displays them to the student.

So the flow is: Browser → Server → APIs → Server → Browser.

The server is basically coordinating everything and keeping the API keys secret - because you definitely don't want to put your API keys in the frontend where anyone can see them.

---

## SLIDE 8: Key Technical Challenges (1.5 minutes)

Of course, building this wasn't all smooth sailing. I ran into some real challenges.

**Challenge 1 was handling long lectures.**

Like, what if someone records a 1-hour lecture? Turns out, APIs have limits. They'll timeout or reject requests that are too big. So I had to implement what's called "chunking." Basically, if a transcript is over 3000 words, I split it into smaller chunks, send each chunk to OpenAI separately, and then combine the results back together. This way, even a 2-hour lecture can be processed without hitting any limits.

**Challenge 2 was audio formats.**

Different browsers record in different formats. Chrome uses WebM, Safari might use WAV, and they have different sample rates and encodings. At first, I was just hardcoding the settings for WebM, but then when I tested on Safari, it broke. So I had to make the server smart enough to detect the file type and adjust the API settings dynamically. Now it just works regardless of what browser you're using.

**Challenge 3 was the user experience during processing.**

30 to 60 seconds doesn't sound like much, but when you're staring at a loading spinner, it feels like forever. So instead of just showing "Loading..." I built a multi-step progress indicator that says exactly what's happening: "Uploading Audio...", "Transcribing Audio...", "Structuring Notes..." This way, students know the app is working and roughly how much longer they need to wait.

**And challenge 4 was API costs.**

These APIs aren't free. Google charges per minute of audio, OpenAI charges per token. If I didn't put any limits, someone could upload a 10-hour recording and rack up a huge bill. So I added hard limits - 25,000 words maximum, 50 MB file size - with clear error messages explaining why the limit exists. This keeps costs reasonable while still handling any normal lecture.

---

## SLIDE 9: Live Demo (2-3 minutes)

Alright, enough talking. Let me actually show you the app in action.

**[Open browser to localhost:5173]**

So here's the interface. Pretty clean and simple. You've got a big "Start Recording" button.

**[Click Start Recording]**

The browser asks for microphone permission - I'll allow that. And now it's recording. You can see the timer counting up. In a real scenario, this would be running during the entire lecture.

**[Play demo lecture or speak for 30 seconds]**

For demo purposes, I'm going to play a short clip from a lecture I recorded earlier.

**[Play audio or speak about a topic]**

"In computer science, algorithms are step-by-step procedures for solving problems. A good algorithm is efficient, correct, and easy to understand. For example, binary search is an algorithm that finds an element in a sorted array by repeatedly dividing the search space in half..."

**[Stop recording]**

Okay, stopping the recording.

Now watch what happens. The app immediately starts processing. See the progress bar? It's showing "Uploading Audio..."

**[Wait and narrate]**

Now it's transcribing... this is where Google's Speech-to-Text is converting the audio to text. This usually takes 20 to 40 seconds depending on the length.

And there we go - "Structuring Notes..." Now OpenAI is organizing the transcript.

**[Results appear]**

Perfect. So here's what we get:

On the left, you have the full transcript. This is exactly what was said, word for word. You can see it says "1,234 words" or whatever the count is.

And below that, you have the structured notes. Look at how it formatted this - it added a main header "Algorithms," then bullet points for the key concepts, bold text for important terms, even a section for examples.

This is way easier to study from than a raw transcript. And the student didn't have to do any of this organization manually - the AI did it all.

**[Scroll through to show formatting]**

You can see headers, bullet points, bold text, all rendered nicely thanks to the markdown formatting.

And if you want to start over and record another lecture, you just click "Start Over" and it resets everything.

Pretty cool, right?

---

## SLIDE 10: What I Learned (1 minute)

So what did I actually learn building this project?

On the technical side, I learned a lot about browser APIs. I didn't even know the MediaRecorder API existed before this project. I learned how to handle binary data - like, what's a Blob, how do you convert it to Base64, all that stuff.

I got really comfortable with async JavaScript - promises, async/await, managing multiple asynchronous operations happening at the same time.

And I learned how to integrate with external APIs. It's not just "here's the data, send it over." You have to handle authentication, rate limits, different error codes, timeouts. It's more complex than it looks.

On the software engineering side, I learned about user-centered design. Every decision I made was based on what would make the student's experience better. Like, should I show a generic spinner or a multi-step progress bar? The progress bar is more work to implement, but it's way better for the user.

I also learned the importance of error handling. Don't just show "Error occurred." Tell the user exactly what went wrong and what they can do about it. "Microphone access denied - please enable microphone permissions in your browser settings." That's way more helpful.

And I learned about prompt engineering for AI. Writing a good prompt for OpenAI isn't just "organize this." You have to be specific: "Create a main title, add an overview, organize into sections with headers, use bullet points for key concepts..." The better your prompt, the better the output.

---

## SLIDE 11: Feasibility Analysis (1 minute)

So the big question: Is this actually feasible? Does it work in the real world?

Short answer: Yes, for typical lectures.

Here's what works really well:
- Clear audio in a quiet classroom
- Single speaker - like a professor lecturing
- Lectures under 30 minutes
- English language content
- Technical or academic topics

These are the scenarios where the app shines. 95%+ accuracy on transcription, great note organization.

But there are definitely limitations right now:
- If the audio is noisy - like lots of background chatter - the accuracy drops significantly
- It can't identify different speakers. So if it's a discussion-based class with lots of student participation, it just lumps everything together
- It's English-only right now, though I could expand that by changing the language code
- There's no database, so if you refresh the page, your notes are gone. That's obviously something I need to add
- And it requires internet - can't work offline

But overall, for a typical college lecture - professor speaking clearly in a quiet room - this works great. It's absolutely feasible.

---

## SLIDE 12: Future Enhancements (1 minute)

Where do I want to take this project next?

In the short term - like next semester - I want to add user accounts so you can save your lecture history and come back to it later. PDF export would be huge - students could download their notes and print them or share them.

Real-time transcription would be awesome too - seeing the text appear as the professor speaks, like live captions. And speaker identification for discussion-based classes.

Medium-term, I want to build mobile apps. The browser version works on phones, but a native app would have better audio recording capabilities and could run in the background.

Multi-language support is on the roadmap. And video upload - right now it's audio-only, but a lot of students record video, so I should support extracting audio from video files.

Long-term, I have some research ideas. What if the app could automatically generate quiz questions from the lecture? Or create one-page summaries of the key concepts? Or even build concept maps showing how different topics relate to each other?

And imagine if this integrated with Canvas or Blackboard - you could automatically save your notes to the course page.

There's a lot of potential here.

---

## SLIDE 13: Project Continuation (45 seconds)

So how am I actually going to continue this?

Over winter break, my plan is to add a database - probably PostgreSQL - so notes are saved permanently. And implement user authentication with JWT tokens so people can have accounts.

I also want to improve the mobile experience. Right now it works on phones, but it's not optimized for them.

Next semester, I'm planning to actually deploy this to production. The frontend will go on Vercel, the backend on Railway - both have free tiers that are perfect for a student project like this.

And then I want to beta test it with real students. Get feedback, see what breaks, fix bugs, iterate.

If it goes well, I might apply for a university innovation grant or partner with campus IT to pilot it in some classes.

And honestly, if there's enough demand, this could potentially become a student startup. But we'll see. First priority is just making it work really well.

---

## SLIDE 14: Reflection (1 minute)

Looking back, what made this project successful?

First, I was solving a real problem that I personally experienced. I wasn't just building something for the sake of building it. I genuinely wanted this tool to exist because I needed it.

Second, I didn't try to reinvent the wheel. I used Google's API, OpenAI's API, React, Express - all proven technologies. I focused on putting them together in a useful way, not on building everything from scratch.

Third, I avoided scope creep. I didn't try to add every feature I could think of. I focused on the core functionality: record, transcribe, structure, display. That's it. And it works.

And the documentation helped a ton. I wrote comments explaining not just what the code does, but why I made certain decisions. When I ran into bugs, those comments saved me hours of debugging time.

If I could do anything differently, I'd start with better error logging from day one. I'd add the database from the beginning instead of as an afterthought. And I'd deploy to a staging environment earlier so I could test in a production-like setup.

But the most valuable lesson? Building something useful is better than building something complex. Students don't care if you used the latest fancy framework. They care if your app saves them time and helps them understand their lectures better.

---

## SLIDE 15: Conclusion & Questions (30 seconds)

Alright, let me wrap this up.

The problem: Students can't listen and take good notes at the same time.

The solution: AI-powered automatic transcription and note structuring.

The tech: React, Express, Google Speech-to-Text, OpenAI GPT.

The result: A working app that takes a lecture recording and gives you organized notes in about a minute.

The impact: Students can focus on learning instead of writing. Lectures are accessible for review anytime. Less stress during fast-paced classes.

And this could be really helpful for students with disabilities - anyone who has trouble taking notes by hand.

Next steps: Deploy it, test it with real users, keep improving it.

That's it. I'm happy to answer any questions you have.

---

## Questions You Might Get Asked (Be Ready For These)

**Q: How much does it cost to run this app?**
A: Right now, for testing, costs are minimal - maybe a few dollars a month. Google charges about $0.006 per 15 seconds of audio, OpenAI charges per token. For a typical 30-minute lecture, it's roughly $0.05-0.10 per lecture. If I deployed this for real and had 100 users, I'd need to think about monetization or getting university funding.

**Q: What if the audio quality is bad?**
A: Yeah, that's a limitation. If there's a lot of background noise or the professor is mumbling, accuracy drops. Best practice is to sit near the front and use an external mic if possible. I could also add a noise reduction preprocessing step in the future.

**Q: Why not use Whisper instead of Google Speech-to-Text?**
A: Great question. Whisper is open source and free, which is awesome. I chose Google because it's more accurate for English and has better punctuation handling out of the box. But I could definitely add Whisper as an alternative option for students who want to avoid API costs entirely.

**Q: How do you handle privacy? Are the lectures stored?**
A: Right now, audio files are deleted immediately after transcription. Transcripts and notes aren't stored in a database - they only exist in the browser session. If I add user accounts, I'd need to add privacy controls and let users delete their data. Definitely something to think about carefully.

**Q: Could this be used for cheating?**
A: I mean, students could record lectures and share notes, but they can already do that without my app. This just makes it easier. I see it more as a study aid - you still have to understand the material to do well on exams. It's like saying calculators enable cheating in math. They're just tools.

**Q: What's the accuracy compared to a human note-taker?**
A: The transcript accuracy is 95%+ for clear audio, which is probably better than human transcription. The note structuring is pretty good - I'd say 80-90% as good as a smart student's notes. The AI sometimes misses nuance or doesn't catch emphasis, but it's solid overall.

---

**Good luck with your presentation! You've got this!**
