# wyze_garmin_sync
Scale sync between Wyze Scale and Garmin connect

This is a script shell allowing you to leverage  [Garmin-uploader](https://github.com/La0/garmin-uploader),[Wyze_SDK](https://github.com/shauntarves/wyze-sdk) and [Garmin Fit SDK](https://developer.garmin.com/fit/fitcsvtool/) fitcsvtool to autoamtically upload the last measure on your Wyze Smart scale in Garmin Connect.

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

I recommend you put yourslef in a specific directory 
```bash
wget https://developer.garmin.com/downloads/fit/sdk/FitSDKRelease_21.67.00.zip
unzip FitSDKRelease_21.67.00.zip
```

#### Installation of the script shell 

Edit the script shell to enter your credentials. 

```bash
WYZE_EMAIL=
WYZE_PASSWORD=
Garmin_username=
Garmin_password=
```
Edit the script to enter the path of the FitCSVTool.jar program

#### Installation of the python script

The script scale.py will query the scale information but you need to add the mac address of the scale for it to work as expected
To discover the mac address rune the script mac_address_devices.py.

```bash
WYZE_EMAIL=
WYZE_PASSWORD=
export WYZE_EMAIL
export WYZE_PASSWORD
python3 ./mac_address_devices.py
```
the script should load all the wyze devices attached to your profile such as below

```bash
product model: WYZECP1_JEF
mac: JA.SC.XXXXXXXXXXXX
nickname: Wyze Scale
is_online: True
product model: JA.SC
```
Take the mac address and edit scale.py to add it like this 
scale = client.scales.info(device_mac='**JA.SC.XXXXXXXXXXXX**')

#### Run the script

In your working directory run connect_sync.sh and if all goes well you should be able to see your data in garmin connect

#### Setup with Cron 

Will run the script every 10 min to get if a new measurment has been made on the scale.

```bash
*/10 * * * * path_to_script/connect_sync.sh 2>&1 | /usr/bin/logger -t garminsync
```
