#### A quick sample, writing both v1.0 and v2.3.0 tags to an MP3 file:

```java
package id3test;

import java.io.*;
import org.blinkenlights.jid3.*;
import org.blinkenlights.jid3.v1.*;
import org.blinkenlights.jid3.v2.*;

public class ID3Test
{
    private static void main(String[] args)
        throws Exception
    {
        // the file we are going to modify
        File sourceFile = new File("some_file.mp3");

        // create an MP3File object representing our chosen file
        MediaFile mediaFile = new MP3File(sourceFile);

        // create a v1.0 tag object, and set some values
        ID3V1_0Tag id3V1_0Tag = new ID3V1_0Tag();
        id3V1_0Tag.setAlbum("Album");
        id3V1_0Tag.setArtist("Artist");
        id3V1_0Tag.setComment("Comment");
        id3V1_0Tag.setGenre(ID3V1Tag.Genre.Blues);
        id3V1_0Tag.setTitle("Title");
        id3V1_0Tag.setYear("1999");
       
        // set this v1.0 tag in the media file object
        mediaFile.setID3Tag(id3V1_0Tag);
       
        // create a v2.3.0 tag object, and set some frames
        ID3V2_3_0Tag id3V2_3_0Tag = new ID3V2_3_0Tag();
        TPE1TextInformationID3V2Frame tpe1 = new TPE1TextInformationID3V2Frame("Lead Performer");
        id3V2_3_0Tag.setTPE1TextInformationFrame(tpe1);
        TRCKTextInformationID3V2Frame trck = new TRCKTextInformationID3V2Frame(3, 9);
        id3V2_3_0Tag.setTRCKTextInformationFrame(trck);
        TIT2TextInformationID3V2Frame tit2 = new TIT2TextInformationID3V2Frame("Song Title");
        id3V2_3_0Tag.setTIT2TextInformationFrame(tit2);
       
        // set this v2.3.0 tag in the media file object
        mediaFile.setID3Tag(id3V2_3_0Tag);
       
        // update the actual file to reflect the current state of our object 
        mediaFile.sync();
    }
}
```

#### Another sample, writing v2.3.0 tags to an MP3 file, using the convenience methods (convenience methods are only available for frames that map to V1.1 settings, which seem to be all many people care about):

```java
package id3test;

import java.io.*;
import org.blinkenlights.jid3.*;
import org.blinkenlights.jid3.v2.*;

public class ID3Test
{
    private static void main(String[] args)
        throws Exception
    {
        // the file we are going to modify
        File sourceFile = new File("some_file.mp3");

        // create an MP3File object representing our chosen file
        MediaFile mediaFile = new MP3File(sourceFile);

        // create a v2.3.0 tag object, and set values using convenience methods
        ID3V2_3_0Tag id3V2_3_0Tag = new ID3V2_3_0Tag();
        id3V2_3_0Tag.setAlbum("Album");  // sets TALB frame
        id3V2_3_0Tag.setArtist("Artist");  // sets TPE1 frame
        id3V2_3_0Tag.setComment("Comment");  // sets COMM frame with language "eng" and no description
        id3V2_3_0Tag.setGenre("Blues");  // sets TCON frame
        id3V2_3_0Tag.setTitle("Title");  // sets TIT2 frame
        id3V2_3_0Tag.setYear(1999);  // sets TYER frame
       
        // set this v2.3.0 tag in the media file object
        mediaFile.setID3Tag(id3V2_3_0Tag);
       
        // update the actual file to reflect the current state of our object 
        mediaFile.sync();
    }
}
```

#### A sample reading tags from an MP3 file, specifically looking for certain properties in v1.0 and v2.3.0 tags:

```java
package id3test;

import java.io.*;
import org.blinkenlights.jid3.*;
import org.blinkenlights.jid3.v1.*;
import org.blinkenlights.jid3.v2.*;

public class ID3Test
{
    private static void main(String[] args)
        throws Exception
    {
        // the file we are going to read
        File sourceFile = new File("some_file.mp3");

        // create an MP3File object representing our chosen file
        MediaFile mediaFile = new MP3File(sourceFile);

        // any tags read from the file are returned, in an array, in an order which you should not assume
        ID3Tag[] id3Tags = oMediaFile.getTags();
        // let's loop through and see what we've got
        // (NOTE:  we could also use getID3V1Tag() or getID3V2Tag() methods, if we specifically want one or the other)
        for (int i=0; i < id3Tags.length; i++)
        {
            // check to see if we read a v1.0 tag, or a v2.3.0 tag (just for example..)
            if (id3Tags[i] instanceof ID3V1_0Tag)
            {
                ID3V1_0Tag id3V1_0Tag = (ID3V1_0Tag)id3Tags[i];
                // does this tag happen to contain a title?
                if (id3V1_0Tag.getTitle() != null)
                {
                    System.out.println("Title = " + id3V1_0Tag.getTitle());
                }
                // etc.
            }
            else if (id3Tags[i] instanceof ID3V2_3_0Tag)
            {
                ID3V2_3_0Tag id3V2_3_0Tag = (ID3V2_3_0Tag)id3Tags[i];
                // check if this v2.3.0 frame contains a title, using the actual frame name
                if (id3V2_3_0Tag.getTIT2TextInformationFrame() != null)
                {
                    System.out.println("Title = " + id3V2_3_0Tag.getTIT2TextInformationFrame().getTitle());
                }
                // but check using the convenience method if it has a year set (either way works)
                try
                {
                    System.out.println("Year = " + id3V2_3_0Tag.getYear());  // reads TYER frame
                }
                catch (ID3Exception e)
                {
                    // error getting year.. if one wasn't set
                    System.out.println("Could get read year from tag: " + e.toString());
                }
                // etc.
            }
        }
    }
}
```

#### In addition to this, your best bet for further details is to have a look at the javadocs, and at the test suites, to see how the library is used.
