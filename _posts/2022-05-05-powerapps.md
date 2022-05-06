# How to get a Power App to use a Huggingface Spaces API

I'd played around with Power Apps and Azure functions so when I had a working Huggingface Spaces application and knew there was also an API I just had to give it a go. It was pretty straightforward, and I've also shared the zip file of the PowerApps solution and the swagger file of the custom connector that needs to be created if you import the solution.  Be aware that custom connectors are defined as *Premium* but it is easy enough to just add a trial to your tenant to give it a try.  You can also sign up for a free tenant with the [Microsoft 365 Developer Program](https://developer.microsoft.com/en-us/microsoft-365/dev-program) if you don't already have access to a tenant.<br>
If you don't want to read any further, but just get the files and start hacking then head over to my Huggingface Space - [Which Raptor?](https://huggingface.co/spaces/LunchWithaLens/whichraptor). I haven't used hf much, but I assume as the Space is public you can get the files.<br>
On with the description of how it all works! The Spaces app is pretty much as described in the fastai course in terms of how to quickly put a classifier into production (not public yet - but will be later in the year) and in my case I chose to identify 5 different common raptors seen on my local walks in Seattle, along with a variant - juvenile bald eagles - which often get confused with Golden Eagles.  Cooper's Hawk, Red-tailed Hawk, Osprey are the others.<br>

 ![The application showing the samples and an identified Cooper's Hawk in my garden](/images/hfSpacess.jpg "Huggingface Spaces - Which Raptor")<br>

To create the Power App I first created the custom connector (or you could use my Swagger file) and I don't intend to do a complete tutorial on creating connectors (Bing it!) but I just took the very basic steps and used the samples available on the Spaces site to set the request and response formats.  I might revisit and add more details if there is interest. Once I had the custom connector I could then create the Power App.  I chose a canvas app in phone format - and just have one screen. I added the custom connector which I'd called whichraptor, then just had the **Add Picture** component and a **Label**.  The label was set to invisible, then made visible once I had a response.  The app supports getting images from either the camera or from wherever on your PC or phone you have pictures.  In my case many are not taken with my phone, but with my FujiFilm X-T4 and 70-300mm lens, then automatically added to my phone.  Identifying a distant bird from a camera phone picture, even a good phone, probably isn't great.<br>

![The application development environment with the interesting stuff showing](/images/powerappdev.jpg "Power Apps screen and app")<br>

The main code is really all in one place and clears the current collection and previous result, if any, and then concerts the chosen image to Base64 encoding and formats to send it off to the API - then gets the result in **Predictions** and sets the top entry in the label and makes it visible.  So the code in OnSelect looks like:<br>

* Clear(Predictions);
* UpdateContext({cVisible: false}); 
* Set(myVarBase64, JSON(UploadedImage1.Image, IncludeBinaryData));
* Set(Base64Json, {Value:myVarBase64});
* Collect(Predictions, whichraptor.Predict({data:Base64Json}));
* UpdateContext({cVisible: true}); 

Then the code in the Answer label's Text property, that is made visible in that last step, is:<br>

* "I think this is a " & First(ThisItem.data).label

Once this is saved and published, you can then add the Power Apps mobile app to your phone and if you sign in to the same tenant you should see you app. (You may need to change the settings to see non-production apps) And you then have a mobile raptor identifier - or whatever you want to build!<br>

![The mobile app - and a good ID of an Osprey](/images/mobilewhichraptor.jpg "An Osprey Identied! (I must fix that 'a')")<br>

The result set also returns the top three highest ranking predictions with the confidence levels - once I get my head around how to pull this out of the json I'll add these and maybe a chart. Enjoy!