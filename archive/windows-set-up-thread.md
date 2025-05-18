NOTE: This is provided as a reference in case the comment goes away or is edited. You don't need to do everything listed here, nor do I think all of it is relevant to most users (Eg, port configuration).


chodthewacko
•
1y ago
•
Edited 6mo ago
You're in luck. I just installed it on windows 10 pro and I was writing up instructions for future reference (when I forget what I did).

The video that mostly showed me what to do was this one: https://www.youtube.com/watch?v=rIBjIWicYZg&t=280s

This is on windows 10 pro. You'll need pro for VM support. Assuming you are, like me, coming in from scratch:

Install docker desktop for windows. get it here:

https://docs.docker.com/desktop/install/windows-install/

This will require a reboot.

After running, it suggested I update to a newer version, and I hit the button to autoupdate to docker 4.28.0.

Make a directory for imm configuration files.

I used: c:\apps\graphics\immich

Grab the config files (docker-compose.yml, example.env) See links on https://immich.app/docs/install/docker-compose Do this by typing the stuff after 'wget' into a browser, which saves them to your download folder.

At the moment the links are: https://github.com/immich-app/immich/releases/latest/download/docker-compose.yml https://github.com/immich-app/immich/releases/latest/download/example.env

Move the two files into c:\apps\graphics\immich rename example.env to .env

I did not download the other two files - you can play with them if you like, but I didn't need them.

Fix port numbers if necessary. Do this:
netsh int ipv4 show excludedportrange protocol=tcp

It shows start-end ranges of port numbers you can't use.

In my case, the relevant parts were:

  2280        2379

  2427        2526
In docker-compose.yml, I had to change ports from 2283:3001 to 2380:3001 since 2283 was blocked.

 Change any ports as needed.
4) Edit .env

 4A) You probably want to change DB_PASSWORD to something random 
(although you never seem to need to type it in later)

4B)  Change UPLOAD_LOCATION to a directory where immich data will be stored.   
(thumbnails, etc). This is also where your pictures uploaded to the immich server will get stored.

I used a new directory I made: UPLOAD_LOCATION=h:\immich_pics\

5) at a command line, in c:\apps\graphics\immich, do: docker compose up -d

This should cause a bunch of stuff to be uploaded/run and you should see several servers get started.

As a sanity check once it is done do:

docker compose down

docker compose up -d
and all you see is something like:

C:\apps\graphics\immich>docker compose up -d

[+] Running 5/6

Network immich_default Created 1.7s

✔ Container immich_redis Started 0.7s

✔ Container immich_postgres Started 0.9s

✔ Container immich_machine_learning Started 0.5s

✔ Container immich_server Started 1.6s

✔ Container immich_microservices Started 1.6s

====

Now try connecting. In a browser go to: http://localhost:2380

Note I used 2380 because I changed the port to 2380 in step 3

You should be at a login screen since it's the first time logging in. Create an account - this will be your admin account.

You should now see something google photoish. You can try uploading a picture here.

EXTERNAL FOLDER instructions:

i.e. You have a bunch of pictures in f:\pictures, and you want use them without uploading them into immich (and doubling your disk space!)

If you are using an external drive/mounted drive, make sure it is mounted and visible before you start Docker, or docker won't see it. Restart docker if necessary.

Edit docker-compose.yml and add

${EXTERNAL_PATH}:/usr/src/app/external

below the ${UPLOAD_LOCATION} line in the 'immich-server' section.

the path doesn't seem to matter, but I think you will need multiple EXTERNAL_PATHs for multiple users.

2) Modify .env

  Add:  
EXTERNAL_PATH=F:\pictures

Note it does every picture/movie in that directory, including subdirectories.

UPLOAD_LOCATION must NOT be inside EXTERNAL PATH or you'll cause an infinite loop.

I.e. don't make UPLOAD_LOCATIONS F:\pictures\uploads.

Side note: I don't know how to add a second external folder with a different EXTERNAL_PATH. I think it's possible though.

3) Authorize a user to use the external drive:

3A) Go to the immich webpage (http://localhost:2380 or whatever)

3B) click on your user icon/circle in the top right corner.

3C) Click on 'Administration'.  On the left side, click on 'external libraries'
    3D)  click on create library (in top right).  select user who owns the library.

	A new row comes up 'new external library'.  

        Click the Blue triple dot circle on the right


	click the triple dots on the right and select 'edit import paths'

	click 'add path'

	Enter:  /usr/src/app/external

	save

3E)  Left menu:  click 'jobs'
           In library, click on 'referesh'
That should load up the library and you should see it grind:

If you open up docker desktop and click 'containers' on the left,

then click the first line: 'immich' you should see a bunch of activity,

most of which are 'Successfully generated thumbnail'.

You can also, in the immich webpage, click 'administration' in the top right corner,

and then 'jobs' on the left menu bar.

You should see a bunch of the jobs change from Active:0 to Active:5 and have a bunch of stuff waiting to be scanned.

Note #2 has a slight delay for initial scan, so check #1 first.

Once it runs for a bit (and it can take a long time), you can reload the web page (or click the flower in the top left corner) and see pictures/map/etc. You'll have a lot of thumbnails that won't exist until it's done though.

I think that covers everything. Let me know how it goes! (apologies for the formatting, i don't have the time right now to figure out reddit formatting.)
