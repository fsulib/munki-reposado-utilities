#!/usr/bin/env python

import os
import sys
import argparse
from lib import FoundationPlist

DEFAULT_CONFIG_PATH = os.path.join(*("Library",
                                    "Preferences",
                                    "com.googlecode.munki.munkiimport.plist"))

def get_sudoer():
    '''Returns sudoer's name, root, or
    False.'''

    sudoer = os.getenv('SUDO_USER')
    uzer = os.getlogin()

    if sudoer or (uzer == 'root'):
        return sudoer or uzer
    else:
        return False


def load_munki_config():
    '''Returns the dict representation of the
    munkiimport configuration plist.'''
    sudoer = get_sudoer()
    if sudoer:
        home = os.getenv('HOME')
        components = [home, DEFAULT_CONFIG_PATH]
        config_path = os.path.join(*components)
        if os.path.isfile(config_path):
            try:
                munki_config = FoundationPlist.readPlist(config_path)
                return munki_config
            except FoundationPlistException, e:
                raise
    else:
        print "You must run this program as root. Exiting."
        sys.exit()


def load_pkginfo(target):
    '''Returns the target plist's path or raises an
    exception'''
    
    munki_config = load_munki_config()

    try:
        results = []
        path = ''
        for root, dirs, files in os.walk(munki_config['repo_path']):
            if target in files:
                path = os.path.join(root, target)
                results.append(path)
                break

        return path, FoundationPlist.readPlist(results[0])
    except IndexError:
        print "File not found."
        sys.exit()
    except:
        raise


def add_to_catalog(pkginfo, catalog):
    '''Adds a catalog to a pkginfo's catalogs array.
    Returns the altered pkginfo.'''

    cat_array = pkginfo['catalogs']
    if catalog in cat_array:
        print "%s is already in this pkginfo's catalogs array." % (catalog,)
        return False
    else:
        cat_array.append(catalog)
        pkginfo['catalogs'] = cat_array
        return pkginfo


def rm_from_catalog(pkginfo, catalog):
    '''Removes a catalog from a pkginfo's catalogs array.
    Returns the altered pkginfo.'''

    cat_array = pkginfo['catalogs']
    if catalog in cat_array:
        cat_array.remove(catalog)
        pkginfo['catalogs'] = cat_array
        return pkginfo
    else:
        print "This pkginfo was not listed in the %s catalog." % (catalog,)
        return False

def write_pkginfo(path, data):
    '''Write the new, altered plist back to file.
    '''
    
    FoundationPlist.writePlist(data, path)


if  __name__ == "__main__":
    
    parser = argparse.ArgumentParser(
            description='Alter a pkgsinfo\'s catalog array')
    parser.add_argument('target', type=str, help='A plist filename.')
    parser.add_argument('--add', type=str, help='Add to this catalog.')
    parser.add_argument('--rm', type=str, help='Remove from this catalog.')
    parser.add_argument('--purge', action="store_false", help='Remove from all catalogs. Doesn\'t work yet.')

    try:
        args = parser.parse_args()
        path, data = load_pkginfo(args.target)
        if args.add:
            new_data = add_to_catalog(data, args.add)
        if args.rm:
            new_data = rm_from_catalog(data, args.rm)
        if new_data:
            write_pkginfo(path, new_data)
        else:
            print "Nothing to do for %s.\nExiting..." % (args.target,)
            sys.exit()
    except Exception as e:
        print "Unexpected error: ", e
