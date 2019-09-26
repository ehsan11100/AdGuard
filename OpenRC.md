# OpenRC service-script

A service-script for OpenRC-based systems, for example if you run AdGuard Home in Alpine (without using Docker).

## Installation

Install OpenRC:

    apk update
    apk add openrc

Then copy the script below to /etc/init.d/adguardhome

    #!/sbin/openrc-run
    #
    # openrc service-script for AdGuardHome
    #
    # place in /etc/init.d/
    # start on boot: "rc-update add adguardhome"
    # control service: "service adguardhome <start|stop|restart|status|checkconfig>"
    #

    description="AdGuard Home: Network-level blocker"

    pidfile="/run/$RC_SVCNAME.pid"
    command="/opt/AdGuardHome/AdGuardHome"
    command_args="-s run"
    command_background=true

    extra_commands="checkconfig"

    depend() {
        need net
        provide dns
        after firewall
    }

    checkconfig() {
        "$command" --check-config || return 1
    }

    stop() {
        if [ "${RC_CMD}" = "restart" ] ; then
            checkconfig || return 1
        fi

        ebegin "Stopping $RC_SVCNAME"
        start-stop-daemon --stop --exec "$command" \
            --pidfile "$pidfile" --quiet
        eend $?
    }

## Usage

Enable running AdGuard Home on boot:

    rc-update add adguardhome

Controlling AdGuard Home:

    service adguardhome <start|stop|restart|status|checkconfig>
