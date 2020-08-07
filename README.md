
## Deploy
1. Create new app

```
heroku create myapp -b https://github.com/The-Antrax/heroku-buildpack-rclone-mod.git
heroku git:clone -a myapp

# or useing existed app
heroku buildpacks:set https://github.com/The-Antrax/heroku-buildpack-rclone-mod.git -a myapp
```

2. Setup Rclone by following [Rclone Docs](https://rclone.org/docs/) 
You can find your config from there:

```
Windows: %userprofile%\.config\rclone\rclone.conf
Linux: $HOME/.config/rclone/rclone.conf
```
Optional: Using service account setup with [Gclone](https://github.com/donwa/gclone) to break Google Drive 750GB limit, or easier connect to folder or Team Drive by destination ID. Create a new folder, such as `/accounts/`, upload your json in it. Open rclone config and edit `service_account_file_path = /app/accounts/` as the json paths.

```
[gdrive_config]
type = drive
client_id = 
client_secret = 
scope = drive
root_folder_id = 
service_account_file = /app/accounts/1.json 
service_account_file_path = /app/accounts/
server_side_across_configs = true
token = {"access_token":"xxxxxxxxxxxxxxx,"token_type":"Bearer","refresh_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxx","expiry":"2020-07-25T21:27:02.0923008+05:30"}
```

3. Go to `myapp` directory, copy `rclone.conf` and winrar registraton key `.rarreg.key` (optional) then commit the change.

```
cd myapp
git add . -f
git commit -am "add config"
git push heroku master
```

## Usage
### Open Terminal
```
cd myapp
heroku run bash
# or
heroku run bash --a myapp
```

### Rclone
Learn more from [Rclone Docs](https://rclone.org/commands/) and [Gclone Docs](https://github.com/donwa/gclone)

**Upload file to Google Drive**
```
# The usual way
rclone -P copy local_dir gdrive_config:remote_dirpath

# The way that like using gclone
rclone -P copy local_dir gdrive_config:{Destination_ID}
```
`-P` mean print progress in real time

### Aria2
Learn more from [Aria2c Docs](http://aria2.github.io/manual/en/html/aria2c.html)

**Download a file**
```
aria2c -x4 http://host/file.rar
```
`-x4` mean download using 4 connection

### UNRAR
Learn more from [UNRAR Docs](https://pypi.org/project/unrar/)

**Extract `.rar` or `.zip` Compressed file**
```
# To current directory
unrar e file.rar/zip

# With full path
unrar x file.rar/zip
```

## Tips

**Speed up upload**

If you want to upload many files smaller than 8mb increase only `--transfers` option
```
rclone -v --transfers=16 --drive-chunk-size=16384k --drive-upload-cutoff=16384k copy local_dir gdrive_config:remote_drive_dir
 ```
`--transfers=N`  number parallel of connection. `default: 4`

` --drive-chunk-size=N` if file bigger than this size it will splits into multiple upload, increase if you want better speed. `default: 8192k or 8mb`

`--drive-upload-cutoff=N` should be same with chunk size

`-v` option to view upload progress stats 
