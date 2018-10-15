Welcome to the AdGuard Home wiki!

The wiki was just recently created, so there isn't much content (yet).

## FAQ

### What is AdGuard Home and why do you need it?

AdGuard Home is a network-wide software for blocking ads & tracking. After you set it up, it'll cover ALL your home devices, and you don't need any client-side software for that. With the rise of Internet-Of-Things and connected devices, it becomes more and more important to be able to control your whole network.

> Please note, that the current state of AdGuard Home is just the beginning. Configuring it requires some technical skills, and we'll try to simplify the process in the future.

## How does it work?

AdGuard Home operates as a DNS server that re-routes tracking domains to a "black hole," thus preventing your devices from connecting to those servers. It's based on software we use for our public [AdGuard DNS](https://adguard.com/en/adguard-dns/overview.html) servers -- both share a lot of common code.

## How to install AdGuard Home?

* [How to install and run AdGuard Home on Raspberry Pi](Raspberry-Pi)
* [How to install and run AdGuard Home on a Virtual Private Server](VPS)

## How do I set up my devices to use my AdGuard Home?

### Router

This setup will automatically cover all the devices connected to your home router, and you will not need to configure each of them manually.

1. Open the preferences for your router. Usually, you can access it from your browser via a URL (like http://192.168.0.1/ or http://192.168.1.1/). You may be asked to enter the password. If you don’t remember it, you can often reset the password by pressing a button on the router itself. Some routers require a specific application, which in that case should be already installed on your computer/phone.
2. Find the DHCP/DNS settings. Look for the ’DNS’ letters next to a field which allows two or three sets of numbers, each broken into four groups of one to three digits.
3. Enter your AdGuard Home server addresses there.

### Android

1. From the Android Menu home screen, tap ’Settings’
2. Tap Wi-Fi on the menu. This will show a list of all of the available networks (it is impossible to set custom DNS for mobile connection)
3. Long press the network you’re connected to, and tap ’Modify Network’
4. On some devices, you may need to check the box for ’Advanced’ to see further settings. To adjust your Android DNS settings, you will need to switch the IP settings from ’DHCP’ to ’Static.’
5. Change ’DNS 1′ and ’DNS 2′ values to your AdGuard Home address.

### iOS

1. From the home screen, tap ’Settings’
2. Choose ’Wi-Fi’ in the left menu (it is impossible to configure DNS for mobile networks)
3. Tap on the name of the currently active network
4. In the ’DNS’ field enter your AdGuard Home address

### macOS

1. Click on Apple icon and go to ’System Preferences.’
2. Click on ’Network’
3. Select the first connection in your list and click ’Advanced.’
4. Select the ’DNS’ tab and add the address of your AdGuard Home.

### Windows

1. Open Control Panel through ’Start’ menu or Windows search
2. Go to ’Network and Internet’ category and then to ’Network and Sharing Center.’
3. On the left side of the screen find ’Change adapter settings’ and click on it
4. Select your active connection, right-click on it and choose ’Properties.’
5. Find ’Internet Protocol Version 4 (TCP/IP)’ in the list, select it and then click on ’Properties’ again
5. Choose ’Use the following DNS server addresses’ and enter your AdGuard Home address there

## Can I throw out traditional ad blockers now?

It depends. "DNS blackholing" is capable of blocking a big chunk of ads, but it lacks flexibility and power of the traditional ad blocking. A good example is [this article](https://adguard.com/en/blog/adguard-vs-adaway-dns66/) that compares `AdGuard for Android` with hosts-level ad blockers. However, this level of protection is enough for some users.