---
title: FAQs
---

# __FAQs__
## **1. Tutorial code stopped working after the latest update.**

This might happen when there is an update to the ThanoSQL syntax. If you have any further questions about this issue, please contact us at contact@smartmind.team and we will look into it as soon as possible. 

## **2. I got an error when using capital letters in ThanoSQL.**

ThanoSQL handles model/column names in lowercase letters. Please use only lowercase letters for model names or column names to call the appropriate model or column.

## **3. When I used the COPY statement to read a csv file, I received an encoding error.**

The COPY clause in ThanoSQL only supports the utf-8 encoding format. For csv files containing Korean, use the COPY clause after the appropriate utf-8 encoding.

## **4. What types of unstructured data does ThanoSQL support?**

ThanoSQL supports the following extensions for image and audio data:

- Image :  "jpg", "png"
- Audio : "wav", "flac", "mp3"
- Video : "mp4"

!!! notice ""
    ThanoSQL does not require any special extensions for text data.
