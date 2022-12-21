# Voice Control in Unity

> 原文：[https://dev.to/thereexistsx/voice-control-in-unity-26b9](https://dev.to/thereexistsx/voice-control-in-unity-26b9)

*(cross-post from my [personal blog](https://expletive-deleted.com/i-enjoy-talking-to-my-computer/))*

Being a Canadian, I can safely say that using your hands in VR still feels like you are trying to interact with something through about five layers of mittens. With the Oculus touch controllers, being able to point a finger to interact with things is leagues better than the not-for-human-hands Vive controllers (save us, knuckle controllers) but most of us don't use just a single finger to type<sup id="fnref1">[1](#fn1)</sup> for a reason.

All this to say, you probably don't want to write your first virtual novel with your virtual hands. And while you would think speaking a novel is also out, it's apparently [not unprecedented](http://www.nytimes.com/2007/01/07/books/review/Powers2.t.html).

Of course, voice recognition comes with its own limitations as anyone who has tried to communicate with a phone menu or "interactive voice response" system can tell you. But microphone quality is increasing, as is the AI that figures out what you are trying to say<sup id="fnref2">[2](#fn2)</sup> . While we might not be there yet, the tech is slowly coming over the horizon.

The primary hurdles in using voices in VR is twofold -- both social aspects. The first, as I discovered when I still worked in an office with other humans, is that it's really awkward to talk to your computer when other people are around. VR already involves flailing around with a huge chunk of plastic on your face, and a cable you must constantly gingerly step around. Voice is fundamentally not private, and even if you are comfortable dictating simple commands in a room full of people,<sup id="fnref3">[3](#fn3)</sup> the noise other people make in a room can create a "mergers and acquisitions" problem<sup id="fnref4">[4](#fn4)</sup>.

The other major problem with voice is that it can skewer social VR. Communicating with other people and telling a machine what to do is just like interleaving all your report writing with your text messages and then sending that report to the person you are texting<sup id="fnref5">[5](#fn5)</sup>. Swapping between command and communication contexts removes the synchronicity of human conversation, and reduces us to the VR equivalent of ignoring that text from your mother to waste time on the internet.

Now that I've spent three paragraphs explaining in detail how much of a pessimist I am, I can safely concede that a single player experience that you can run in a quiet, private setting isn't an extremely tall order for VR. So it's still worth taking seriously.

Voice commands are pretty easy to set up in Unity<sup id="fnref6">[6](#fn6)</sup> if you're working on a Windows machine. You can create a new script in Unity and include the Windows Phrase Recognizer by adding `using UnityEngine.Windows.Speech;` to the top.

When trying to build a project with the phrase recognizer you may encounter an error if you don't have Windows' core speech recognition enabled -- you can fix that by opening the *"Speech, inking & typing"* settings in the Windows Control panel and turning on speech services. Take a look at [Microsoft's documentation](https://developer.microsoft.com/en-us/windows/mixed-reality/voice_input_in_unity) if you still have trouble setting things up.

Using the speech services opens the door to two types of voice recognition: the phrase recognizer, and the dictation recognizer.

The more basic of the two is the phrase recognizer which can be further broken down into two categories -- a keyword recognizer, and a grammar recognizer. With the phrase recognizer you can set up specific words, or rules (grammars) that the project listens for and acts on as soon as it hears. The phrase recognizer is great because it triggers as soon as it recognizes one of its key phrases and runs really quickly.

Implementing a phrase recognizer is pretty easy. Suppose I had a sphere and wanted it to turn blue when I said 'blue', and 'red' when I said red -- the code might look something like this:

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Windows.Speech;

public class PhraseVoiceControl : MonoBehaviour {
    // references to the red and blue materials we want to use -- set in the inspector
    public Material red;
    public Material blue; 

    private KeywordRecognizer keywordRecognizer;
    private string[] keywords;
    private GameObject sphere;

    void Start () {
        // set up a list of keywords we can recognize
        keywords = new string[] { "red", "blue" };
        keywordRecognizer = new KeywordRecognizer(keywords);

        // triggers an event when a phrase is recognized
        keywordRecognizer.OnPhraseRecognized += OnPhraseRecognized;
        keywordRecognizer.Start();
    }

    // event triggered when a phrase is recognized
    void OnPhraseRecognized(PhraseRecognizedEventArgs args){
        // the text of the argument is the word the recognizer thinks you have said
        if(args.text == "red"){
            GetComponent<Renderer>().material = red;
        } else if(args.text == "blue"){
            GetComponent<Renderer>().material = blue;
        }
    }
} 
```

A grammar recognizer is similar but instead of words the phrase recognizer listens for patterns outlined in a formal grammar -- you can learn about setting up a grammar [here](https://msdn.microsoft.com/en-us/library/hh378349(v=office.14).aspx).

The more complicated system is the dictation recognizer. It transcribes the whole phrase you say into a string that you can use, and waits until you have completed a sentence by looking for a significant pause. This is why the dictation recognizer is a lot slower than the phrase recognizer, so if you have single word specific commands you should always use phrases instead. That said, with the entire text of what is spoken you can start to do interesting stuff with AI by sending the text off to an NLP API<sup id="fnref7">[7](#fn7)</sup>.

Getting started with dictation recognition in Unity is very similar, we'll use the same example as above:

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Windows.Speech;

public class DictationVoiceControl : MonoBehaviour {
    private DictationRecognizer dictationRecognizer;

    public Material red;
    public Material blue;

    void Start () {
        dictationRecognizer = new DictationRecognizer();

        dictationRecognizer.DictationResult += Sentences;
        dictationRecognizer.Start();
    }

    private void Sentences(string text, ConfidenceLevel confidence){
        // the arguments are the full text of what the recognizer things you said, and how confident it is in that assessment
        if(text.Contains("red")){
            GetComponent<Renderer>().material = red;
        } else if(text.Contains("blue")){
            GetComponent<Renderer>().material = blue;
        }
    }
} 
```

This is not a great use case for the dictation recognizer because we have particular words that we know we want to switch on. However, now that you have the full text of what is spoken it can then be processed by an NLP library or hooked into some kind of bot to generate responses to queries.

While getting started with voice in Unity is pretty easy, it's quite powerful and I've really only been able to scrape the surface here<sup id="fnref8">[8](#fn8)</sup>. Natural language processing, and bot writing, are textbook length material though something I am starting to dig into. I would absolutely recommend talking to computer and only worry about it talking back if you *haven't* built a bot<sup id="fnref9">[9](#fn9)</sup>

* * *

1.  I still cannot touch type, AMA. [↩](#fnref1)

2.  "Fuck this goddamn machine to hell." [↩](#fnref2)

3.  Hey, extroverts, good to see you here. [↩](#fnref3)

4.  Yeah, I did make an American Psycho joke there. [↩](#fnref4)

5.  Or, my nerdy readers, like trying to type your code in vim's command mode. [↩](#fnref5)

6.  And a dark hellscape in Unreal. [↩](#fnref6)

7.  I decided I wanted to consume APIs instead of adding plugins and went with Microsoft's [LUIS](https://www.luis.ai) over IBM's [Watson](https://www.ibm.com/watson/) because I didn't really want to make an IBM account. [↩](#fnref7)

8.  I've been really trying to lay off the magnum opus blog posts. [↩](#fnref8)

9.  I told myself I wasn't going to make this joke but there it is. [↩](#fnref9)