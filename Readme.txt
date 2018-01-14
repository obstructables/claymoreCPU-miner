Claymore's CryptoNote CPU Miner.
=========================

This is POOL version.

This version is for Windows x64, Windows x86 is supported up to v3.4 only. No Linux support planned.

This version uses AES-NI if CPU supports it, but also works with older CPUs that don' support AES-NI.

This miner is free-to-use, however, current developer fee is 2.5%, miner mines 39 rounds for you and 1 round for developer. If you use encrypted connection ("ssl://") the developer fee is 2%.
If you don't agree with dev fee - don't use this miner, or use "-nofee" option.
Attempts to cheat and remove dev fee will cause a bit slower mining speed (same as "-nofee 1") though miner will show same hashrate.



COMMAND LINE OPTIONS:

-o 	pool address. Both HTTP and Stratum protocol are supported. You can specify several "-o" parameters to use several pools, or use "pools.txt" file, or use both. 
	First pool specified via "-o" option is main pool: miner will switch to main pool every 30 minutes.
	Miner also supports SSL/TLS encryption for all data between miner and pool (if pool supports encryption), it significantly improves security.
	To enable encryption, use "ssl://" or "stratum+ssl://" prefix (or "tls" instead of "ssl").

-u 	your wallet address.

-p 	password, use "x" as password.

-t 	number of threads. "-t 0" - autoselection. Autoselection does not work fine in all cases, so try different values.
	Optimal value depends mostly on L3 cache size. 
	For example, if your CPU has 8 MB of L3 cache (i7 CPUs), use "-t 4". For 6MB L3 cache (i5 CPUs) use "-t 3".

-allpools Specify "-allpools 1" if miner does not want to mine on specified pool (because it cannot mine devfee on that pool), but you agree to use some default pools for devfee mining. 
	Note that if devfee mining pools will stop, entire mining will be stopped too.

-lowcpu	low CPU usage mode. In this mode only one CPU thread is used but the speed is much higher than in "-t 1" mode.
	This mode is useful for mining in background when minimal CPU usage is required instead of maximal mining speed. 
	Possible values are "-lowcpu 1" or "-lowcpu 2". For example, on i7 4770 CPU "-lowcpu 2" shows about 180 h/s on a single CPU thread.
	This option is available only for CPUs that support AES-NI.

-ee 	close miner if no more pools are available in the list. By default, miner tries all pools one by one, after last pool it tries first pool again and so on. 
	Use "-ee 1" to close miner when it tried all pools, so you can restart it from some script and do some additional actions related to internet connectins if necessary.

-dbg	debug log and messages. "-dbg 0" (default) - create log file but don't show debug messages. 
	"-dbg 1" - create log file and show debug messages. "-dbg -1" - no log file and no debug messages.

-nofee: set "1" to cancel my developer fee at all. In this mode some recent optimizations are disabled so mining speed will be slower by about 5%. 
	By enabling this mode, I will lose 100% of my earnings, you will lose only 2.5% of your earnings.
	So you have a choice: "fastest miner" or "completely free miner but a bit slower".
	If you want both "fastest" and "completely free" you should find some other miner that meets your requirements, just don't use this miner instead of claiming that I need 
	to cancel/reduce developer fee, saying that 2-2.5% developer fee is too much for this miner and so on.

-r	Restart miner mode. "-r 0" (default) - restart miner if something wrong. "-r -1" - disable automatic restarting. -r >0 - restart miner if something 
	wrong or by timer. For example, "-r 60" - restart miner every hour or when some worker thread failed.

-retrydelay	delay, in seconds, between connection attempts. Default values is "20". Specify "-retrydelay -1" if you don't need reconnection, in this mode miner will exit if connection is lost.

-ftime	failover main pool switch time, in minutes, see "Failover" section below. Default value is 30 minutes, set zero if there is no main pool.

-mport	remote monitoring/management port. Default value is -3333 (read-only mode), specify "-mport 0" to disable remote monitoring/management feature. 
	Specify negative value to enable monitoring (get statistics) but disable management (restart, uploading files), for example, "-mport -3333" enables port 3333 for remote monitoring, but remote management will be blocked.
	You can also use your web browser to see current miner state, for example, type "localhost:3333" in web browser. 
	Warning: use negative option value or disable remote management entirely if you think that you can be attacked via this port!
	By default, miner will accept connections on specified port on all network adapters, but you can select desired network interface directly, for example, "-mport 127.0.0.1:3333" opens port on localhost only.

-mpsw	remote monitoring/management password. By default it is empty, so everyone can ask statistics or manage miner remotely if "-mport" option is set. You can set password for remote access (at least EthMan v3.0 is required to support passwords).

-colors enables or disables colored text in console. Default value is "1", use "-colors 0" to disable coloring. Use 2...4 values to remove some of colors.

-v	displays miner version, sample usage: "-v 1".



CONFIGURATION FILE

You can use "config.txt" file instead of specifying options in command line. 
If there are not any command line options, miner will check "config.txt" file for options.
If there is only one option in the command line, it must be configuration file name.
If there are two or more options in the command line, miner will take all options from the command line, not from configuration file.
Place one option per line, if first character of a line is ";" or "#", this line will be ignored. 
You can also use environment variables in "epools.txt" and "config.txt" files. For example, define "WORKER" environment variable and use it as "%WORKER%" in config.txt or in epools.txt.



SAMPLE USAGE

unsecure connection:
	NsCpuCNMiner64.exe -o stratum+tcp://mine.moneropool.org:80 -u 449TGay4WWJPwsXrWZfkMoPtDbJp8xoSzFuyjRt3iaM4bRHdzw4qoDu26FdcGx67BMDS1r2bnp7f5hF6xdPWWrD3Q3Wf7G6 -p x

SSL/TLS connection:
 miningpoolhub (this pool detects encryption automatically so it uses same port as for unencrypted connection):
	NsCpuCNMiner64.exe -o ssl://us-east.cryptonight-hub.miningpoolhub.com:20580 -u YourLogin.YourWorker -p x

Do not forget to specify your wallet address!



FAILOVER

Use "epools.txt" file to specify additional pools. This file has text format, one pool per line. Every pool has 3 connection attempts. 
Miner disconnects automatically if pool does not send new jobs for a long time or if pool rejects too many shares.
If the first character of a line is ";" or "#", this line will be ignored. 
Do not change spacing, spaces between parameters and values are required for parsing.
If you need to specify "," character in parameter value, use two commas - ,, will be treated as one comma.
Pool specified in the command line is "main" pool, miner will try to return to it every 30 minutes if it has to use some different pool from the list. 
If no pool was specified in the command line then first pool in the failover pools list is main pool.
You can change 30 minutes time period to some different value with "-ftime" option, or use "-ftime 0" to disable switching to main pool.



REMOTE MONITORING/MANAGEMENT

Miner supports remote monitoring/management via JSON protocol over raw TCP/IP sockets. You can also get recent console text lines via HTTP.
Start "EthMan.exe" from "Remote management" subfolder (Windows version only).
Check built-in help for more information. "API.txt" file contains more details about protocol.



PERFORMANCE

About 280 h/s on i7-4770 ("-t 4")
About 170 h/s on i5-4430 ("-t 3")
32bit version is slower than 64bit version in 1.5-2.0 times, about 190 h/s on i7-4770.



TROUBLESHOOTING

For most cases miner shows detailed error messages with explanations. To achieve maximal mining speed, start miner with admin rights once (miner must show "scfg: 1"), 
it will configure system for optimal performance; then reboot computer to apply changes. For normal work no admin rights or other permissions are required. However, 
if you use Windows UAC and start miner as admin in non-elevated mode miner will not work. Either create normal user and start miner there, or disable UAC.
Miner must show "FAST MODE ENABLED" message if everything is ok.
Sometimes reboot is necessary to clean RAM, otherwise miner can show "not enough memory" error.

Low speed in Windows 8.1 x64:

1. Make sure you are logged as admin. Create shortcut for NsCpuCNMiner64.exe on desktop.
2. Open shortcut properties, and specify command line parameters, for example:
C:\miner\NsCpuCNMiner64.exe -o stratum+tcp://mine.moneropool.org:80 -u 449TGay4WWJPwsXrWZfkMoPtDbJp8xoSzFuyjRt3iaM4bRHdzw4qoDu26FdcGx67BMDS1r2bnp7f5hF 6xdPWWrD3Q3Wf7G6 -p x
3. Press "Advanced" button, check "Run As Administrator". Also disable UAC and reboot (perhaps this step is not ncessary for your configuration).
4. Start shortcut, I get about 290 h/s on stock i7-4770 in Windows 8.1 x64.



FAQ:

Q: Why do I see more shares for devfee than in my mining for the same time?
A: Most pools support variable diff, they change "target share" after some time after connection. For example, you have very powerful rig, after connection you will send shares very often. It takes some CPU time to check your shares so after some time pool will send higher share target and miner will send less shares (but they will have more value). When pool updates share target you will see "Pool sets new share target" line in the miner. This way pool can adjust the number of shares that miner sends and balance its load.
So check the log or console text to see current target for main mining thread and for devfee thread. For example:
DevFee: Pool sets new share target: 0x0083126e (diff: 500H) - this is for devfee mining connection
Pool sets new share target: 0x0024fa4f (diff: 1772H) - this is for main mining connection
As you can see, target share for main mining is higher in about 3.5 times, so for main mining miner sends in 3 times less shares (but they have 3x more value) than for devfee mining.

Q: Miner freezes if I put cursor to its window in Windows 10 until any key is pressed. Sometimes miner freezes randomly until any key is pressed.
A: You should make some changes in Windows:
  https://superuser.com/questions/555160/windows-command-prompt-freezing-on-focus
  https://superuser.com/questions/419717/windows-command-prompt-freezing-randomly?rq=1
  https://superuser.com/questions/1051821/command-prompt-random-pause?rq=1

