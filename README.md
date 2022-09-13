# pulp-deb not synching all packets to content units

## basic setup

Debian 11
pulp from PyPi (using ansible)

ansible-vars are documented in the `recreate/` folder.

## pulp objects

all details in the `state`-folder

1 remote: `bullseye-backend`, mirroring `deb.debian.org/debian` with distributions `bullseye`, `bullseye-updates`, `bullseye-backports`, `bullseye-backports-sloppy`

1 repository: `bullseye`

synced using:
~~~bash
curl -s -u "$PULPUSER:$PULPPASS" -H "Content-Type: application/json" localhost/pulp/api/v3/repositories/$REPOID/sync/ -d '{"remote":"'$REMOTEID'"}'
~~~

1 distribution (using publication `deb/verbatim`): `bullseye`

## Behaviour

The package index (see `state/`) contains all packets available upstream.

A couple of packages do not have content units and can't be installed. The ones I know about:

- ifenslave
- krb5-locales
- libc-l10n
- locales
- dpkg-dev
- libdpkg-perl
- ifenslave
- publicsuffix

I found these because of errors during `apt upgrade` (404's).

I've noticed that pulp-deb doesn't create content units for those packages (see `state/packagesearches.txt`).

## reproduce

~~~bash
$ ansible-galaxy collection install pulp.squeezer pulp.pulp_install
$ ansible-galaxy role install geerlingguy.postgresql
$ ansible-playbook recreate/playbook.yaml -e @recreate/ansible-vars.yml
~~~
