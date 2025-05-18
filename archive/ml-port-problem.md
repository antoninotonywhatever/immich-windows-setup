u/ustolemyname avatar
ustolemyname
•
4mo ago
When you access http://localhost:3003/ from your desktop browser you should see: {"message":"Immich ML"}

Similary from another computer or phones browser for http://192.168.0.5:3003

If the first isn't working, docker is probably not configured to expose port 3003 for the container

If only the second isn't, it's possibly a firewall or port binding issue not allowing incoming connections from other computers.

Hope that helps!



Upvote
1

Downvote

Reply
reply

Award

Share
Share

u/Ok-Consideration5602 avatar
Ok-Consideration5602
OP
•
4mo ago
How silly of me, I just assumed the compose file on the Immich website included the port exposure by default.. It didn't. 🙂

It's working now, I just need to see if /how I can configure it to use hardware acceleration.

Thanks for your help.



Upvote
1

Downvote

Reply
reply

Award

Share
Share

u/ustolemyname avatar
ustolemyname
•
4mo ago
Welcome!

The compose file sets it up so immich-server and the machine learning container are on the same private network. So by default it's not exposed.


Upvote
1

Downvote

Reply
reply

Award

Share
Share

cmdr_cathode
•
3mo ago
could you elaborate on how exactly you got it to work? I have setup immich-ml-remote on a local machine via podman (because fedora). I get {"message":"Immich ML"} by both going to localhost:3003 on a local browser and when accesing the local IP from another machine but immich reports "fetch failed"



Upvote
1

Downvote

Reply
reply

Award

Share
Share

u/Ok-Consideration5602 avatar
Ok-Consideration5602
OP
•
3mo ago
If you see the message it means you have correctly exposed the port on your remote machine.. If it does not work you have either entered the details wrong in Immich, or Immich does not have access to the IP in question..

I've got this in my Immich settings (set as the first value in the list):

http://192.168.0.5:3003

Perhaps check the logs to see if it says anything there?



Upvote
1

Downvote

Reply
reply

Award

Share
Share

cmdr_cathode
•
3mo ago
Heya! Thanks for your help. It magically started working after some time. Whatever the issue was...
