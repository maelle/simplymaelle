---
title: "Storytime preparedness with av"
date: '2020-12-10'
tags:
  - av
slug: av-storytime
output: hugodown::hugo_document
rmd_hash: e8e59991a2c32426

---

My kids got a cool electronic storyteller as a gift. It is basically a pretty cube that you shake to make it play tracks. [*La conteuse merveilleuse*](https://www.joyeuse.io/) comes with pre-loaded songs and stories, but you can also add your own. Very handy, as we had e.g. CDs that came with magazines. According to the anti-manual[^1], to add a mp3 or wav file you own, you first need to convert it to the storyteller's expected format by using the company's online converter, *la moulinette* (the mill). In this post I shall explain how I, a cool anti-mom, used R, in particular the av package, instead.

Official guidance on how to convert audio files on the Conteuse merveilleuse
----------------------------------------------------------------------------

The storyteller can be plugged into a computer like an USB stick, using its specific red cable. The interface of the storyteller is the file system, which is rather intuitive to me. There is a folder for each face of the cube. When you shake the cube three times with e.g. the lion face facing upwards, it plays a random track from the LION folder.

As a side note, something that's fascinating to me is that the settings of the cube, like the maximal volume, are to be tweaked via a text file called settings.txt! Not even reglages.txt! Furthermore, your storyteller's ID (needed to register the device on the company's website) is stored as a text filename.

You can buy new tracks from the company's website, record your own tracks in their studio, and upload your local stories to the website for then getting them as compatible mp3. You are limited to 5 conversions a day, of files up to 100Mb. I had some connection issues and couldn't even get a few conversions, and besides, some of the tracks (from our [local kid theater](http://www.lacachette.fr/lacachette_nancy.php), unsurprisingly currently closed) I wanted to use were a bit bigger than 100Mb.

Using R instead
---------------

When reading the FAQ on the website, sadly not linkable as it's in the website section where you're logged in, I saw a sentence describing the mp3 formats for "geeks": *mono, 64 kbps, 16 khz, constant bitrate*. Maybe I felt targeted by the word geek, in any case seeing this made me realize I could probably use other tools to convert tracks. I remembered about Jeroen Ooms' av package, and decided to read its docs, in particular of [`av::av_audio_convert()`](https://docs.ropensci.org/av/reference/encoding.html).

Now, I still had no clear idea what all the numbers in the format description meant, but I realized av has a function very similar to [`magick::image_info()`](https://docs.ropensci.org/magick/reference/attributes.html): [`av::av_media_info()`](https://docs.ropensci.org/av/reference/info.html)!

I used it on a track pre-loaded on the cube.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span class='c'># I did not add the space character</span>
<span class='nf'>av</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/av/reference/info.html'>av_media_info</a></span><span class='o'>(</span><span class='s'>"/media/maelle/JOYEUSE-AD/FR/NUAGE/Je respire.mp3"</span><span class='o'>)</span>

<span class='c'>#&gt; $duration</span>
<span class='c'>#&gt; [1] 426.564</span>
<span class='c'>#&gt; </span>
<span class='c'>#&gt; $video</span>
<span class='c'>#&gt; NULL</span>
<span class='c'>#&gt; </span>
<span class='c'>#&gt; $audio</span>
<span class='c'>#&gt;   channels sample_rate    codec frames bitrate layout</span>
<span class='c'>#&gt; 1        1       16000 mp3float     NA   64000   mono</span>
</code></pre>

</div>

This helped me see the parameter values of [`av::av_audio_convert()`](https://docs.ropensci.org/av/reference/encoding.html) I needed to use: `channels = 1` and `sample_rate = 16000`. I did not tweak the `bitrate`, I didn't see how, and anyway my tracks had a lower bit rate (so lower quality) than the company's ones.

After the conversion I'll describe later, here's the info I got about new tracks.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span class='nf'>av</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/av/reference/info.html'>av_media_info</a></span><span class='o'>(</span><span class='s'>"/media/maelle/JOYEUSE-AD/FR/LION/choum.mp3"</span><span class='o'>)</span>

<span class='c'>#&gt; $duration</span>
<span class='c'>#&gt; [1] 265.356</span>
<span class='c'>#&gt; </span>
<span class='c'>#&gt; $video</span>
<span class='c'>#&gt; NULL</span>
<span class='c'>#&gt; </span>
<span class='c'>#&gt; $audio</span>
<span class='c'>#&gt;   channels sample_rate    codec frames bitrate layout</span>
<span class='c'>#&gt; 1        1       16000 mp3float     NA   24000   mono</span>

<span class='nf'>av</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/av/reference/info.html'>av_media_info</a></span><span class='o'>(</span><span class='s'>"/media/maelle/JOYEUSE-AD/FR/ROUGE/leila.mp3"</span><span class='o'>)</span>

<span class='c'>#&gt; $duration</span>
<span class='c'>#&gt; [1] 762.984</span>
<span class='c'>#&gt; </span>
<span class='c'>#&gt; $video</span>
<span class='c'>#&gt; NULL</span>
<span class='c'>#&gt; </span>
<span class='c'>#&gt; $audio</span>
<span class='c'>#&gt;   channels sample_rate    codec frames bitrate layout</span>
<span class='c'>#&gt; 1        1       16000 mp3float     NA   24000   mono</span>
</code></pre>

</div>

No need to tell av what format to use as the `output` filename extension gives it away.

I wrote two conversions functions

-   One for tracks coming with a magazine, to crop out the intro song. This was crucial for user-friendliness of the cube as the title of the story comes after the 41 seconds (admittedly very cute) intro song which is a long time without knowing whether you want to listen or shake again to get another random track.

``` r
convert_belles <- function(audio_path) {
  av::av_audio_convert(
    audio = audio_path,
    output = file.path("output", basename(audio_path)),
    channels = 1,
    start_time = 41,
    sample_rate = 16000
  )
}
```

-   One for other tracks needing no cropping, but including some "wav" files.

``` r
convert_autres <- function(audio_path) {
  av::av_audio_convert(
    audio = audio_path,
    output = file.path("output", gsub("wav", "mp3", basename(audio_path))),
    channels = 1,
    sample_rate = 16000
  )
}
```

I could have written a single function instead with one more parameter. :shrug:

To apply the functions

-   I put the tracks in two folders (by hand).
-   For each folder I used [fs](https://fs.r-lib.org/) and [purrr](https://purrr.tidyverse.org/).

``` r
to_convert <- fs::dir_ls("input/belles-h")

convert_belles <- function(audio_path) {
  av::av_audio_convert(
    audio = audio_path,
    output = file.path("output", basename(audio_path)),
    channels = 1,
    start_time = 41,
    sample_rate = 16000
  )
}

purrr::walk(
  to_convert,
  convert_belles
)
```

After that I manually copy-pasted output tracks to the face folders of the cube. Now the tracks corresponding to theater stories are all in the ROUGE face for instance.

In reality I started by converting *one* track, then put it in a face folder of the cube and tried playing it.

Conclusion
----------

In this post I presented my small script converting mp3 and wav tracks to mp3 compatible with my offspring's storyteller. To me these were a few empowering lines of utility code. I think it exemplifies the usefulness of [av](https://docs.ropensci.org/av) for audio manipulation. Now, maybe I'll go record some tale about R to put on my kids' cube...

[^1]: Apparently when you're a cool new startup you don't call your docs a manual!

