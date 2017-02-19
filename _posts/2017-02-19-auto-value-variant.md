---
layout: post
published: true
title: auto-value-variant
---
## An AutoValue Extension


{% highlight java %}
public class Album {

    private String mTitle;
    private String mArtist;
    private String mDescription;
    private String mImage;

    public Album(String title, String artist, String description, String image) {
        mTitle = title;
        mArtist = artist;
        mDescription = description;
        mImage = image;
    }

    public String getTitle() {
        return mTitle;
    }

    public String getArtist() {
        return mArtist;
    }

    public String getDescription() {
        return mDescription;
    }

    public String getImage() {
        return mImage;
    }
}
{% endhighlight %}

Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.
