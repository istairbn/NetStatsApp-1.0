#NetStatsApp-1.0
Creator: Ben Newton
Contact: Ben.newton@excelian.com

This app is designed to pump network data out in a JSON format, suitable for ingestion by Logscape. It covers every connection on the box and out puts the State, PID, remote and local ports and addresses, process name and protocol. It's main use case is for when you are suffering from Network issues and need to know how many connections you have and what ports are actually in use. 

It can generate around 200MB data if you run it at a high frequency (ie every 10 seconds - which is great for live diagnostics), but running it once a minute gives you sufficient oversight most for general use.  

To set up - Simply zip all the files up into a zip file called NetStatsApp-1.0.

NOTE - Check the lib/NetStates.properties waitTime variable first - see below.

Files:
powerShellRunner.groovy = The java script that runs a powershell process as a service in Logscape. 
NetStatsApp-1.0.bundle = The bundle file for Logscape use. I've left it set to Windows Hosts only, you may wish to limit it to the boxes you need monitoring. 
NetStats.config = The data type and watch directory for Logscape.
lib/NetStates.properties = A simple properties file, used for config. The two options you can change are:

waitTime =  Time between polling in seconds. 10 for example gives you excellent real time oversight on your network at a high data volume cost. 60 is a decent compromise for live style analytics. 300 (5 minutes) is enough for you to set up alerts on dropped connections. 

passArgs = If you want to limit the output, use the passArgs to tell the cmdlet what you want to use.  

lib/NetStats.ps1

The Powershell that does the work. A big thanks to Cookie Monster and Shay Levy who actually wrote this code, all I did was give it a wrapper suitable for use in a monitoring solution - here is their documenation to help understand the features included. As you can see, the various parameters can be used as filters with the passArgs variable in the config file.



    .SYNOPSIS
	    Display current TCP/IP connections for local or remote system

    .FUNCTIONALITY
        Computers

    .DESCRIPTION
	    Display current TCP/IP connections for local or remote system.  Includes the process ID (PID) and process name for each connection.
	    If the port is not yet established, the port number is shown as an asterisk (*).	
	
    .PARAMETER ProcessName
	    Gets connections by the name of the process. The default value is '*'.
	
    .PARAMETER Port
	    The port number of the local computer or remote computer. The default value is '*'.

    .PARAMETER Address
	    Gets connections by the IP address of the connection, local or remote. Wildcard is supported. The default value is '*'.

    .PARAMETER Protocol
	    The name of the protocol (TCP or UDP). The default value is '*' (all)
	
    .PARAMETER State
	    Indicates the state of a TCP connection. The possible states are as follows:
		
	    Closed       - The TCP connection is closed. 
	    Close_Wait   - The local endpoint of the TCP connection is waiting for a connection termination request from the local user. 
	    Closing      - The local endpoint of the TCP connection is waiting for an acknowledgement of the connection termination request sent previously. 
	    Delete_Tcb   - The transmission control buffer (TCB) for the TCP connection is being deleted. 
	    Established  - The TCP handshake is complete. The connection has been established and data can be sent. 
	    Fin_Wait_1   - The local endpoint of the TCP connection is waiting for a connection termination request from the remote endpoint or for an acknowledgement of the connection termination request sent previously. 
	    Fin_Wait_2   - The local endpoint of the TCP connection is waiting for a connection termination request from the remote endpoint. 
	    Last_Ack     - The local endpoint of the TCP connection is waiting for the final acknowledgement of the connection termination request sent previously. 
	    Listen       - The local endpoint of the TCP connection is listening for a connection request from any remote endpoint. 
	    Syn_Received - The local endpoint of the TCP connection has sent and received a connection request and is waiting for an acknowledgment. 
	    Syn_Sent     - The local endpoint of the TCP connection has sent the remote endpoint a segment header with the synchronize (SYN) control bit set and is waiting for a matching connection request. 
	    Time_Wait    - The local endpoint of the TCP connection is waiting for enough time to pass to ensure that the remote endpoint received the acknowledgement of its connection termination request. 
	    Unknown      - The TCP connection state is unknown.
	
	    Values are based on the TcpState Enumeration:
	    http://msdn.microsoft.com/en-us/library/system.net.networkinformation.tcpstate%28VS.85%29.aspx
        
        Cookie Monster - modified these to match netstat output per here:
        http://support.microsoft.com/kb/137984

    .PARAMETER ComputerName
        If defined, run this command on a remote system via WMI.  \\computername\c$\netstat.txt is created on that system and the results returned here

    .PARAMETER ShowHostNames
        If specified, will attempt to resolve local and remote addresses.

    .PARAMETER tempFile
        Temporary file to store results on remote system.  Must be relative to remote system (not a file share).  Default is "C:\netstat.txt"

    .PARAMETER AddressFamily
        Filter by IP Address family: IPv4, IPv6, or the default, * (both).

        If specified, we display any result where both the localaddress and the remoteaddress is in the address family.

    .EXAMPLE
	    Get-NetworkStatistics | Format-Table

    .EXAMPLE
	    Get-NetworkStatistics iexplore -computername k-it-thin-02 -ShowHostNames | Format-Table

    .EXAMPLE
	    Get-NetworkStatistics -ProcessName md* -Protocol tcp

    .EXAMPLE
	    Get-NetworkStatistics -Address 192* -State LISTENING

    .EXAMPLE
	    Get-NetworkStatistics -State LISTENING -Protocol tcp

    .OUTPUTS
	    System.Management.Automation.PSObject

    .NOTES
	    Author: Shay Levy, code butchered by Cookie Monster
	    Shay's Blog: http://PowerShay.com
        Cookie Monster's Blog: http://ramblingcookiemonster.wordpress.com

    .LINK
        http://gallery.technet.microsoft.com/scriptcenter/Get-NetworkStatistics-66057d71
