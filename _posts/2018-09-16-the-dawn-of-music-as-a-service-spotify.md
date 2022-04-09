---
layout: post
title: "The Dawn of Music as a Service: Spotify"
date: 2018-09-16
summary: Content recommendation has become like a clingy neighbor.
---

If you've spent any time at all browsing content on the internet, your
experience has been manipulated by content recommendation. The banner ads about
the shoes you just Googled, the related recommendations for the YouTube video
you just watched, the suggestions in your Netflix homepage --- there's no
getting around it. Content recommendation has become like the clingy neighbor
that somehow manages to end up at all the same social events as you, always in
your face. You hate to admit it, but sometimes they're helpful --- most of the
time just creepy, though.

What happens when this clingy neighbor gets into your music library? Today we'll
be going over the case of music recommendation in the digital age, focusing on the
industry leader --- Spotify.

### The Dawn of the Digital DJ

If you're unfamiliar with the name, Spotify is a music streaming service that
has spearheaded the recent disruption of the music industry, shifting the
listening experience from digital downloads and album purchases to on-demand
music streaming. Spotify does more than just stream songs, though --- they
recommend new music to you based on the heaps of data they collect from their
users.

Their front-runner? *Discover Weekly*, an algorithmically-curated
playlist made individually for each and every user, refreshed every week to
keep up with their developing personal tastes. The results are usually quite
good, sometimes even uncanny:

![tweet-discover-weekly](/assets/images/dw-tweet.png)
<sub>Figure 1: https://twitter.com/dave_horwitz/status/659084401691615232?lang=en</sub>

For myself, the results are about right up there: I'd say 70-80% of suggested
songs end up becoming a part of my personal library. Obviously, this system
isn't perfect, but it's quite near, and it has turned the music discovery
process on its head. Old music discovery was tattered maps and shifty
compasses, and Spotify just gave us GPS.

### Till There Was You

I'm not that old --- depending on your scale, you could probably call me a Gen
Z or a Millennial. For that reason, my childhood was still largely digital. I
didn't have to discover music by digging around for tapes or vinyls or even
word-of-mouth live shows. I was never restricted to the music my town was known
for or the radio stations my parents liked (at least not for my *whole* life).
I had YouTube.

That's correct, before music had made its way to Spotify, a **lot** of it was
on YouTube. In the early days, the video-sharing platform was littered with
simple "lyric videos" for music. The only problem was that it wasn't organized
very well, so you had to look up songs you knew. If you were lucky, you'd find
a good song by digging around in the "Related Videos" section from songs you
already knew, but that usually took some time and effort.

I prided myself on finding cool new music on YouTube. I'd spend countless hours
clicking away, listening and listening until I found something amazing. It was
a good feeling, like I was this pioneer of personal playlists.

There was a certain attachment I had to the music that came from the experience
of searching for it. I'd show my friends songs I "discovered", and I'd have my
little moments of fame. I could recommend artists to them based on what they
currently listened to, like a human version of Discover Weekly.

Some years later, Spotify made its debut. I initially just used the service for
convenience --- it was easy to just stream the audio alone, no video attached
to it. I had heard of Discover Weekly, but at the time, it was unrefined and
spotty. I held to my ways for a while, but that all started to change as the
platform started to mature. Friends would rave about the playlist, so I started
to try it out to surprising success. For someone whose ears tend to always have
headphones in them, new music was a treat. A couple months on the playlist, and
the habit had formed: Monday mornings were spent in auditory elation, Sunday
evenings in eager anticipation.

### The Secret Sauce

So how does this ~~wizardry~~ algorithm even work? 

![dw-data-flow](/assets/images/dw-data-flow.png)
<sub>Figure 2: Data Flow of Discover Weekly Playlist [8]</sub>

Well doesn't that look inviting! Don't worry, we're not actually concerned with
the entire flow of data for today. We're actually going to analyze a much
*friendlier* sub-section of this model: the flow of the Discover Weekly
playlist itself.

![dw-data-flow-sub](/assets/images/dw-data-flow-sub.png)
<sub>Figure 3: Data Flow of Discover Weekly Playlist (modified) [8]</sub>

There we go, much better. Let's start analyzing our sub-section here. Notice
that it goes right to left instead of left to right. Starting on the right
side, we see three boxes: "Batch CF Models", "Batch NLP Models", and "Batch
Audio Models". These three "models" then seem to converge in the
"Recommendations Pipeline", which ultimately outputs a familiar face: Discover
Weekly. Let's break down these terms.

We'll start with "Batch CF Models". CF stands for **Collaborative Filtering**.
This is actually the simplest part of the process. Let's say you're Spotify,
and you've got some number of songs (30+ million). Knowing information like
what songs and artists the user has been listening to, how do you choose which
songs out of the 30+ million to suggest to a given user? Well, you could look
at how similar people are using Spotify and make guesses (Fig. 4).

For example, let's say Jim listens to a lot of Drake, Future, and Travis Scott.
Spotify notices Taylor (a friend of Jim) listens to those artists, but she also
listens to Flatbush Zombies. Knowing that they have similar preferences, you
would then suggest that Jim listen to Flatbush Zombies. In practice, Spotify
does this at scale, depending upon *collaborative* preference data from
millions of users to *filter* choices of songs to suggest to them [1].

![collaborative-filtering-visual](/assets/images/collaborative-filtering-visual.png)
<sub>Figure 4: Non-technical illustration demonstrating process of collaborative
filtering [8].</sub>

Now, Collaborative Filtering is great for very popular music with tons of usage
data to learn from, but it falls apart when there's less playback data
available on the music. This could be because the song is usually listened to
elsewhere (Soundcloud) or simply that the music isn't very well-known. This is
where the second dimension of the algorithm comes in --- "Batch NLP Models".
NLP stands for **Natural Language Processing** [2]. This type of recommendation
is independent of the user's usage data and instead relies on text ---
*language* --- related to music to find correlations between them.

For Spotify, this text is usually found in news/written media or song metadata
(metadata: data about your data)[2]. This means that when music journalists
publish something like [album
reviews](https://pitchfork.com/reviews/albums/20913-sun-coming-down/), Spotify
can *process* the descriptive words written about different albums to see if
their relative definitions correlate. This means albums associated with words
like "edgy" might be more closely associated with "aggressive" albums than
something like a "soft" album. Simpler versions of this are also used --- for
example, to generate a playlist, Spotify might choose a descriptive title like
"Coffee Table Jazz" and then write a description filled with associative words.
Spotify can compare the words associated with specific songs (title, lyrics,
artist name, album name) to see if they match the words associated with the
playlist and then add them based on how similar they are.

Now we've got two key recommendation models here that seem to be adequate for
most cases, but what about music that 1. has little to no playback data and 2.
is not publicized in written media (or even lyricized)? What about completely
*new* music from unsigned artists, people that don't have a record label or
established following? This is where the least developed (and most innovative)
component of the process comes in: "Batch Audio Models".

Perhaps the simplest to summarize and the hardest to explain, these **audio
models** process the *audio in the music* and attempt to make meaningful
inferences about them. This is done via machine-learning, specifically deep
neural networks --- essentially training computers to classify audio files,
similar to the way some programs are trained to recognize faces in pictures
[1]. This involves picking out specific sound patterns (bass drums, vibrato
singing, chord progressions) so that similar songs can be grouped together
(Fig. 5). Note here that this is based on *audio patterns*, not necessarily
genre classifications. These associations created between songs can also
ultimately be used to suggest new music in Discover Weekly.

![audio-clustering-visual](/assets/images/audio-clustering-visual.png)
<sub>Figure 5: Groupings of different songs based on similar audio patterns. Pretty
cool, right? [1]</sub>

Let's recap really quickly here --- we've got a system that relies on 3 key
models to make its music recommendations, based on 1. what other people listen
to, 2. things people have written about the music, and 3. the actual audio
itself. In practice, the exact combination of these methods is unknown, so you
may have music suggestions that rely on any combination of the models.

### Algorithms or Authenticity?

If this all feels very *engineered*, that's because it is. Data is the key
driver here --- not "good music taste" or some artful combination of cheap
whiskey, cigarettes, and tattoos.

For a long while, the accuracy of these playlists bothered me. Finding new
music the old way was a lot of hard work --- it was very personal. Part of the
connection I had to the music was rooted in the journey of finding it.
Sometimes I'd find songs in my Discover Weekly that I already knew, songs that
I had "discovered" in the old days. Seeing those familiar titles strewn about
--- it was like someone had rifled through my desk and found all my little
knick-knacks. Sometimes Discover Weekly felt cold, calculated, like some
mass-produced *product*. I didn't like that.

Then I learned the *how* and *why* of it all: how this algorithm worked and
more importantly, *why* Spotify was trying so hard to do this in the first
place. Spotify's mission is to "unlock the potential of human creativity --- by
giving a million creative artists the opportunity to live off their art and
billions of fans the opportunity to enjoy and be inspired by it" [3]. Now I
understand that "mission statements" are often little more than PR candy ---
it's the action that matters here, so what have Spotify's efforts actually led
to?

### Genre Spotlight: Anti-Pop

![anti-pop-visual](/assets/images/anti-pop-visual.png)
<sub>Figure 6: A Spotify-curated playlist, Anti Pop.
https://open.spotify.com/user/spotify/playlist/37i9dQZF1DWWqNV5cS50j6</sub>

If you've never heard of this genre, you may recognize some if its main
artists: Rex Orange County, Billie Eilish, BROCKHAMPTON, Cuco, or Still Woozy.
With a sound vaguely reminiscent of mainstream pop music and a soul hell-bent
on defying the very same mainstream, this genre has amassed steady growth
amongst the millennial generation.

Most, if not all, of this growth can be attributed to the rise of the streaming
era and music recommendation. These artists are usually unsigned, often working
out of their bedrooms with DIY set-ups.

BROCKHAMPTON is a boy-band that got together over internet forums dedicated to
Kanye West. Rex Orange County is a 19-year old DIY artist whose self-released
albums have gotten him the attention of established artists like Tyler the
Creator. Cuco is another 19-year old self-produced artist specializing in
low-key romantic melodies harmoniously intertwined with his Chicano background.
Connected to the rest of the world via Spotify and other streaming platforms,
these artists are part of a new age of internet-based musicians achieving
success simply because *people like their music*. This popularity is the sole
source of their success, not record labels or enormous live performances, and
this popularity is largely driven by streaming platforms like Spotify that
recommend *new content* to their users.

### To Connect

When I look through my current music library, I see a lot of influences that I
can really only attribute to Spotify's recommendations. Does the fact that it
was recommended to me by some algorithm make it ingenuine? I used to see it
that way. But now when I look through my music, I just see *artists* whose
music I deeply enjoy. Artists that I can now support *in real life, right now*.
I've got a connection, a digitally-enabled connection.

We live in a world where we make a lot of digital connections: Facebook
friends, Instagram followers, Linkedin connections. These connections are made
so easily that at times they can feel cheap, superficial. Is it the
responsibility of the platform to make these connections genuine? I think not
--- that onus has always been in the hands of the *user*.

---

#### References
<!-- Numbers were escaped because they formatted oddly. -->
1\. Van den Oord, Aaron, Sander Dieleman, and Benjamin Schrauwen. "Deep
   content-based music recommendation." In Advances in neural information
   processing systems, pp. 2643-2651. 2013.

2\. Mikolov, Tomas, Ilya Sutskever, Kai Chen, Greg S. Corrado, and Jeff Dean.
   "Distributed representations of words and phrases and their
   compositionality." In Advances in neural information processing systems, pp.
   3111-3119. 2013.

3\. Spotify AB, "Company Info", *spotify.com*. [Online]. Available:
   <https://newsroom.spotify.com/companyinfo/> [Accessed 13 September 2018]

4\. Jacobson, Kurt, Vidhya Murali, Edward Newett, Brian Whitman, and Romain Yon.
   "Music personalization at Spotify." In Proceedings of the 10th ACM
   Conference on Recommender Systems, pp. 373-373. ACM, 2016.

5\. Whitman, Brian, and Tristan Jehan. "Determining the similarity of music
   using cultural and acoustic information." U.S. Patent 8,073,854, issued
   December 6, 2011.

6\. Mattsson, Andreas. "Methods and systems for providing media recommendations
   based on implicit user behavior." U.S. Patent 9,659,068, issued May 23,
   2017.

7\. A. Pasick, "The magic that makes Spotify's Discover Weekly playlists so damn
   good", Quartz Magazine, December 21, 2015 [Online]. Available:
   https://qz.com/571007/the-magic-that-makes-spotifys-discover-weekly-playlists-so-damn-good/
   [Accessed 13 September 2018]

8\. C. Johnson, "From Idea to Execution: Spotify's Discover Weekly", University
   of Texas at Austin, November 16, 2015

9\. V. Murali, "Building Data Pipelines for Music Recommendation at Spotify",
   University of Wisconsin-Madison, October 17, 2015
