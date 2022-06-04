# wyze_garmin_sync


First and foremost, I am not the creator of the project nor am I the maintainer. The project is owned and maintained by the original owner svanhoutte and can be found [here.](https://github.com/svanhoutte/wyze_garmin_sync) I have only updated a few things to make the script work.


Scale sync between Wyze Scale and Garmin connect

This is a script shell allowing you to leverage  [Garmin-uploader](https://github.com/La0/garmin-uploader),[Wyze_SDK](https://github.com/shauntarves/wyze-sdk) and [Garmin Fit SDK](https://developer.garmin.com/fit/fitcsvtool/) fitcsvtool to automatically upload the last measure on your Wyze Smart scale in Garmin Connect.

*I removed the 2FA as i want the script to run automatically without any input, then i recommand extra strong password and to take all the precautions you need.*

### Requirements 

This requires Python 3.8 and above. If you're unsure how to check what version of Python you're on, you can check it using the following:

> **Note:** You may need to use `python3` before your commands to ensure you use the correct Python path. e.g. `python3 --version`

```bash
python --version

-- or --

python3 --version
```
You ll need to have also Java8 or 1.8 installed (oracle or open java are working fine)

```bash
java -version

```
You ll need also a linux environment mine is an Ubuntu 20.04.2 but most of the recent distrib should work. 
I am personally using dietpi bullseye on a spare raspberry pi. 

### Installation

#### Installation of Wyze SDK

```bash
$ pip install wyze_sdk
```
#### Installation of Garmin-uploader

```bash
$ pip install garmin-uploader
```

#### Installation of Garmin Fit SDK 

I recommend you put yourslef in a specific directory. I have chosen to make a Wyze folder at root or ~. 
```bash
cd ~ && mkdir Wyze && cd Wyze
wget https://developer.garmin.com/downloads/fit/sdk/FitSDKRelease_21.78.00.zip
unzip FitSDKRelease_21.78.00.zip
```
#### Downloading Wyze_Garmin_Sync from git using wget
In the same wyze directory run these commands to download git scripts
```bash
wget https://github.com/Thatindianbruh/wyze_garmin_sync/archive/refs/heads/main.zip
unzip main.zip
```
#### Installation of the script shell 

Edit the connect_sync.sh to enter your credentials. 

cd into the wyze_garmin_sync-main directory.
```bash
cd wyze_garmin_sync-main
nano connect_sync.sh
```
replace text after = with your credentials. 

```bash
WYZE_EMAIL=YourEmail
WYZE_PASSWORD=YourPassword
Garmin_username=YourEmail
Garmin_password=YourPassword
```
Edit the script to enter the path of the FitCSVTool.jar program

scroll down and find this line.
```bash
java -jar /path_to_the_tool/FitCSVTool.jar  -c ./wyze.1.csv ./wyze.fit
```

replace path_to_the_tool with actual directory. 

Since I am using the Wyze folder at root. My command looks like this. 

```bash
java -jar ~/Wyze/java/FitCSVTool.jar -c ./wyze.1.csv ./wyze.fit
```
save and exit nano with ctrl+x and Y

#### Installation of the python script

The script scale.py will query the scale information but you need to add the mac address of the scale for it to work as expected
To discover the mac address open the wyze scale device info in the wyze app.

Take the mac address and edit scale.py and replace X's with the MAC address.

```bash 
nano scale.py
scale = client.scales.info(device_mac='**JA.SC.XXXXXXXXXXXX**')
```
save and exit nano with ctrl+x and Y

#### Run the script

In your working directory run connect_sync.sh and if all goes well you should be able to see your data in garmin connect

```bash
./connect_sync.sh
```
This is what you should get back. 

```bash
File is correct.
md5sum: ./wyze.last.csv: No such file or directory
./wyze.last.csv: FAILED open or read
md5sum: WARNING: 1 listed file could not be read
FIT CSV Tool - Protocol 2.0 Profile 21.78 Release
./wyze.1.csv encoded into FIT binary file ./wyze.fit.
fit file created
2022-06-03 19:58:57,840 [DEBUG] File '/root/Wyze/wyze_garmin_sync-main/wyze.fit' has extension '.fit'
2022-06-03 19:58:57,841 [DEBUG] File '/root/Wyze/wyze_garmin_sync-main/wyze.fit' extension '.fit' is valid.
2022-06-03 19:58:57,841 [DEBUG] Using credentials from command line.
2022-06-03 19:58:57,842 [INFO] Try to login on GarminConnect...
2022-06-03 19:58:57,842 [DEBUG] Username: your_email
2022-06-03 19:58:57,842 [DEBUG] Password: ***********************
2022-06-03 19:58:58,510 [DEBUG] Found CSRF token "bunch of random numbers"
2022-06-03 19:59:02,633 [INFO] Logged in as your_email
2022-06-03 19:59:02,634 [DEBUG] Login Successful.
2022-06-03 19:59:02,900 [INFO] Uploaded activity wyze.fit
2022-06-03 19:59:02,900 [INFO] All done.
file uploaded
```
if it says file uploaded and you can confirm that stats have updated in the garmin app, only then continue with the cron job. 

if it says 
```bash 
File is correct.
./wyze.last.csv: OK
no new measurment
```
This means that there is no new update from the scale and everything is ok. 

If you want to force a new update, simply delete the wyze.last.csv by using the rm command. "rm wyze.last.csv"

#### Setup with Cron 

let's run the script every 15 min to get if a new measurement has been made on the scale.

open crontab to edit cron jobs with 
```bash
crontab -e
```
replace /directory_of_the_script/ and insert the line at the very bottom. 

```bash
*/15 * * * * cd /directory_of_the_script/ && sudo sh connect_sync.sh 2>&1 | /usr/bin/logger -t garminsync
```
Since I'm using the Wyze folder at root, My command looks like this. 

```bash
*/15 * * * * cd /root/Wyze/wyze_garmin_sync-main && sudo sh connect_sync.sh 2>&1 | /usr/bin/logger -t garminsync
```
To check if the script is working you can check logs with journalctl and look for garminsync and output. 
