# Video player with seeking in DSpace / XMLUI

The implementation described below uses _videojs_ - _https://videojs.com/_

- Video format is mp4
- Videos are stored in a folder accessible by your browser.

## Web server settings
1. I am assuming you are using Apache to front your tomcat
2. You forward requests to tomcat with ProxyPass

You need to allow pass-through access for you apache web server (video requests must not be forwarded to tomcat)
```
ProxyPass /video_folder !
AliasMatch ^/video_folder/(.*) /folder1/subfolder1/video_folder/$1
```

Add the following to item-view.xsl
```
 <xsl:if test="dim:field[@element='relation' and @qualifier='uri' and descendant::text()]">
                    <xsl:variable name="fileURL" select="dim:field[@element='relation' and @qualifier='uri']" />
                    
                    <link href="https://vjs.zencdn.net/7.5.4/video-js.css" rel="stylesheet"></link>

                    <!-- If you'd like to support IE8 (for Video.js versions prior to v7) -->
                    <script src="https://vjs.zencdn.net/ie8/1.1.2/videojs-ie8.min.js"></script>
                    
                    <xsl:variable name="imageURL" select="concat('https://{FQDN}/video_folder/', $fileURL, '.jpg')" />
                    <xsl:element name="video">
                        <xsl:attribute name="id">my-video</xsl:attribute>  
                        <xsl:attribute name="class">video-js vjs-big-play-centered</xsl:attribute>
                        <xsl:attribute name="controls"></xsl:attribute>
                        <xsl:attribute name="preload">auto</xsl:attribute>
                        <xsl:attribute name="width">540</xsl:attribute>
                        <xsl:attribute name="height">264</xsl:attribute>
                        <xsl:attribute name="poster"><xsl:value-of select="$imageURL" /></xsl:attribute>
                        <xsl:attribute name="data-setup">{}</xsl:attribute>
                        <xsl:variable name="concatURL" select="concat('https://{FQDN}/video_folder/', $fileURL, '.mp4')" />
                        <xsl:element name="source">
                            <xsl:attribute name="src"><xsl:value-of select="$concatURL" /></xsl:attribute>  
                            <xsl:attribute name="type">video/mp4</xsl:attribute>
                        </xsl:element>
                        

                        <p class="vjs-no-js">
                            To view this video please enable JavaScript, and consider upgrading to a web browser that
                            <a href="https://videojs.com/html5-video-support/" target="_blank">supports HTML5 video</a>
                        </p>
                    </xsl:element>
                    
                    <script src='https://vjs.zencdn.net/7.5.4/video.js'></script>
                    
                </xsl:if>
```

create video poster with ffmpeg like
```
ffmpeg -ss 00:04:07 -i video.mp4 -vframes 1 poster_image.jpg
```


For this to work you records must have a dc.relation.uri with the location of a video file, but omit the file extension (.mp4).
