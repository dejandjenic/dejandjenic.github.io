title: How to embed video
created: Sunday, July 9, 2023
tags:
  - genesysSSG
  - video
short: How to embed video in your statically generated web site
image: /images/genesysssg_video.png
---
### How to Embed Videos

Videos can greatly enhance your website or blog. Whether you want to embed videos from YouTube, Vimeo, or any other online platform, or even host them locally, here's how you can do it.

#### Embedding YouTube Videos

To embed a YouTube video, use the following Markdown syntax:

```markdown
[![Video Title](https://img.youtube.com/vi/YOUR_VIDEO_ID/0.jpg)](https://www.youtube.com/watch?v=YOUR_VIDEO_ID)
```


sample output

![youtube.com](https://www.youtube.com/watch?v=mswPy5bt3TQ)

If you want to start the video from a specific point, you can add the time parameter in the video URL:

```
[![Video Title](https://img.youtube.com/vi/YOUR_VIDEO_ID/0.jpg)](https://www.youtube.com/watch?v=YOUR_VIDEO_ID&t=100)
```


sample output

![youtube.com with t](https://www.youtube.com/watch?v=mswPy5bt3TQ&t=100)

You can also use the short syntax for YouTube videos:

```
[![Video Title](https://youtu.be/YOUR_VIDEO_ID)](https://youtu.be/YOUR_VIDEO_ID)
```


sample output

![youtu.be](https://youtu.be/mswPy5bt3TQ)

#### Embedding Local Videos
If your video is hosted locally, you can use the following Markdown syntax:


[![Video Title](https://sample.com/video.mp4)](https://sample.com/video.mp4)

That's it! You can now embed videos in your Markdown-based static site generator using these instructions.
