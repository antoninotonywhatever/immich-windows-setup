This assumes you've got WSL and Docker Compose Configured. 

- Make a directory, cd into it, and grab the following
  - `wget -O docker-compose.yml https://github.com/immich-app/immich/releases/latest/download/docker-compose.yml`
  - `wget -O .env https://github.com/immich-app/immich/releases/latest/download/example.env`
  - `wget -O hwaccel.ml.yml https://github.com/immich-app/immich/releases/latest/download/hwaccel.ml.yml`
  - `wget -O hwaccel.transcoding.yml https://github.com/immich-app/immich/releases/latest/download/hwaccel.transcoding.yml`

Configure each of the files according to these instructions. You can also find these files with configuration in this repo, but you shouldn't just copy those in, I provided them for reference. 

- Configuring `.env`


- Configuring `docker-compose.yml`
  -  Uncomment and appropriately configure the `extends` sections for `hwaccel.transcoding.yml` and `hwaccel.ml.yml`
    - The files uploaded here are using Nvidia and CUDA, adjust accordingly for your hardware, or skip it entirely if you don't care
  - Add a path under `volumes` for `EXTERNAL_PATH`s, following what you did in the `.env` file
    - For example: `${EXTERNAL_PATH}:/usr/src/app/external` this is the location where an external directory will be stored on the immich server
    - You can add more, according to what's in your env
  - Add a range to `ports` for `3003:3003`
    - This is necessary for some ML tasks. Before adding this, I'd have weird intermittent issues with facial detection not running




Sources / What helped me along in configuration. Storing archives of these posts in `sources/` in case they go away. 
- [Immich docker compose docs](https://immich.app/docs/install/docker-compose/), [Immich Hardware Transcoding Docs](https://immich.app/docs/features/hardware-transcoding/),[ Immich Hardware-accelerated Machine Learning Docs](https://immich.app/docs/features/ml-hardware-acceleration/)
- [This Reddit Thread](https://www.reddit.com/r/immich/comments/1b5u6p2/comment/ktn7g2p/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button)
  - General config stuff, includes additional troubleshooting
-[ This Reddit Thread](https://www.reddit.com/r/immich/comments/1i9un56/comment/m95fey1/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button) for opening a given port to make ML features work consistently
- [This Blogpost](https://www.ephestione.it/immich-manually-download-machine-learning-ai-models/)
  - For troubleshooting ML/AI issues
