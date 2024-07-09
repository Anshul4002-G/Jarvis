# voice assisstant jarvis



#  jarvis

import speech_recognition as sr
import webbrowser
import pyttsx3
import musiclibrary
import requests
from openai import OpenAI
from gtts import gTTS #defines speak function by google cloud
import pygame

# helps to recognise the voice called by user 
recognizer = sr.Recognizer()
engine=pyttsx3.init()
newsapi="https://api.marketdata.app/v1/stocks/quotes/SPY/"

# this function helps the jarvis to respond on the user command 
def speak_old(text):
    engine.say(text)
    engine.runAndWait()

# gtts based function helps the jarvis to respond on the user command 
def speak(text):
   tts=gTTS(text)
   tts.save("temp.mp3")
   pygame.init()
   pygame.mixer.init()


# starting message made by system
# Load the MP3 file (might require OGG format for wider compatibility)
   pygame.mixer.music.load("temp.mp3")

# Optionally set volume (between 0.0 and 1.0)
   pygame.mixer.music.set_volume(1.8)

# Play the music
   pygame.mixer.music.play()

# Wait for the music to finish playing (optional)
   while pygame.mixer.music.get_busy():
     pygame.time.Clock().tick(10) # Check for events (optional)

# Quit Pygame (optional)
   pygame.quit()
  

#  the function defines the role and details of user and system 
def aiprocess(command): # api subcription required to perform this function 
   
   client=OpenAI(
     api_key="sk-proj-jnRxeS5FUg3DwOJtUN7hT3BlbkFJ9pTFwnXaOeZZGDYJ93O7",
     )
   completion = client.chat.completions.create(
   model="gpt-3.5-turbo",
   messages=[
        {"role":"sysytem" ,"content":"you are a poetic assistent , skilled in explianing complex problems sh as alexa "},
        {"role":"user","content":command}
       ]
   )
   return completion.choices[0].message.content


#  task that can be called to jarvis to perform 
def processcommand(c):
   
  if "open google" in c.lower():
      webbrowser.open("https://google.com/")

  elif "open facebook" in c.lower():
      webbrowser.open("https://facebook.com")

  elif "open linkedin" in c.lower():
      webbrowser.open("https://linkedin.com")

  elif "open github" in c.lower():
      webbrowser.open("https://github.com")

  elif "open instagram" in c.lower():
      webbrowser.open("https://instagram.com")

  elif "open helper" in c.lower():
      webbrowser.open("https://gemini.google.com/app?gad_source=1")


  elif "open youtube" in c.lower():
      webbrowser.open("https://www.youtube.com/watch?v=M4KA0sB14Eo")

  elif "open Github" in c.lower():
      webbrowser.open("https://github.com/")

  elif "news" in c.lower():
      r= requests.get(f"https://newsapi.org/v2/top-headlines?country=in&apikey={newsapi}")
      if r.status_code==200:
         data = r.json()

         articles=data.get('article',[])

         for article in articles:
            speak(article['title'])

  elif c.lower().startswith("play"):
      song=c.lower().split(" ")[1]
      link=musiclibrary.music[song]
      webbrowser.open(link)


  else:
      # let open ai ghandle the request
      output=aiprocess(c)
      speak(output)



if __name__ == "__main__":
    speak("initialising jarvis")
    while(True):

    #  listen for the weak word "jarvis" 
    # obtain audio from the microphone

      r=sr.Recognizer()
    #  recognize speech using 
      print("recognising")
      try:
        with sr.Microphone() as source:
          print("Listenning....")
          audio = r.listen(source , phrase_time_limit=1)
        word = r.recognize_google(audio)
        if(word.lower()=="jarvis"):
           speak("ya")
        #  listen for command 
           with sr.Microphone() as source:
            print("jarvis active")
            audio = r.listen(source)
            command=r.recognize_google(audio)
            processcommand(command)

      except Exception as e:
        print("error; {0}".format(e))

    
    


