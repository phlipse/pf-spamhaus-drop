# pf-spamhaus-drop

The script [pf-spamhaus-drop](https://github.com/phlipse/pf-spamhaus-drop/blob/master/10.pf-spamhaus-drop) uses the [Don't Route Or Peer Lists (DROP)](https://www.spamhaus.org/drop/) from Spamhaus to block all traffic to and from listed IPs. The lists get daily updated through the [periodic utility](https://www.freebsd.org/cgi/man.cgi?periodic).

It downloads the wanted lists, IPv4 lists only or also IPv6, and persists them in a table file for pf. After that it reloads the table to let pf know about the new entrys.

**This script is for the periodic utility and only works on FreeBSD with pf enabled!**

## Usage
1. Copy the script to */usr/local/etc/periodic/daily/*:

    ```
    $ sudo cp 10.pf-spamhaus-drop /usr/local/etc/periodic/daily/
    $ sudo chmod 555 /usr/local/etc/periodic/daily/10.pf-spamhaus-drop
    ```

    Or get it as [raw file](https://raw.githubusercontent.com/phlipse/pf-spamhaus-drop/master/10.pf-spamhaus-drop) and paste it into your terminal. Don't forget to change permissions!

2. Enable it in */etc/periodic.conf.local*:

    ```
    $ sudo vi /etc/periodic.conf.local
        # ipv4
        daily_pf_spamhaus_drop_enable="YES"
        # uncomment if using ipv6
        #daily_pf_spamhaus_drop_ipv6="YES"
    ```

3. Adjust pf configuration, usually */etc/pf.conf*:

    ```
    $ sudo vi /etc/pf.conf
        table <spamhausdrop> persist file "/etc/pf.spamhausdrop"
        block in log quick on $if from <spamhausdrop> to any
        block out log quick on $if from any to <spamhausdrop>
    ```

    **Adjust $if to your public facing interface!**

    If you don't want pf to log every single block, you can simply remove the keyword *log*.

4. Touch */etc/pf.spamhausdrop* and verify configuration of pf:

    ```
    $ sudo touch /etc/pf.spamhausdrop
    $ sudo pfctl -nf /etc/pf.conf
    ```

4. Enjoy it daily in your periodic report! :)

5. If you want to, execute it on demand:

    ```
    $ sudo /usr/local/etc/periodic/daily/10.pf-spamhaus-drop
    ```

## License

Use of this source code is governed by the [MIT License](https://github.com/phlipse/pf-spamhaus-drop/blob/master/LICENSE).
