#!/usr/bin/python3
# `mpreg` program by ANW for MrrpOS v2
# The MrrpOS package/misc downloader

import requests
import os
import sys

print("/------------------------------------------------------\\")
print("| mpreg v2 - The MrrpOS package/misc. files downloader |")
print("| By ANW, for MrrpOS. (https://anw.is-a.dev)           |")
print("\\------------------------------------------------------/\n")

# Define constants
PACKAGE_LIST_URL = "https://mrrpos.pages.dev/pkgs/pkg-list.json"
MISC_LIST_URL = "https://mrrpos.pages.dev/misc/misc-list.json"

def fetch_json_data(url):
    try:
        response = requests.get(url)
        response.raise_for_status()
        return response.json()
    except requests.exceptions.RequestException as e:
        print(f"Error fetching data from {url}: {e}", file=sys.stderr)
        return None

def download_file(url, destination_folder=".", filename=None):
    if not os.path.exists(destination_folder):
        os.makedirs(destination_folder)

    if filename is None:
        filename = os.path.basename(url)

    filepath = os.path.join(destination_folder, filename)

    try:
        with requests.get(url, stream=True) as r:
            r.raise_for_status()
            with open(filepath, 'wb') as f:
                for chunk in r.iter_content(chunk_size=8192):
                    f.write(chunk)
        print(f"Downloaded '{filename}' to '{destination_folder}'")
        return True
    except requests.exceptions.RequestException as e:
        print(f"Error downloading '{url}': {e}", file=sys.stderr)
        return False
    except IOError as e:
        print(f"Error writing file '{filepath}': {e}", file=sys.stderr)
        return False

def list_packages(pkg_data):
    if not pkg_data:
        print("No packages found or unable to retrieve package list.")
        return

    print("\n--- Available Packages ---")
    for pkg_name, details in pkg_data.items():
        print(f"  Name: {pkg_name}")
        print(f"    Version: {details.get('version', 'N/A')}")
        print(f"    Description: {details.get('description', 'N/A')}")
        print(f"    Author: {details.get('author', 'N/A')}")
        print(f"    Language: {details.get('lang', 'N/A')}")
        print(f"    Type: {details.get('type', 'N/A')}")
        print(f"    Dependencies: {details.get('dependencies', 'none')}")
        print(f"    Download URL: {details.get('download', 'N/A')}")
        print("-" * 30)

def list_misc_files(misc_data):
    if not misc_data:
        print("No miscellaneous files found or unable to retrieve misc list.")
        return

    print("\n--- Available Miscellaneous Files ---")
    for category, items in misc_data.items():
        print(f"  Category: {category.capitalize()}")
        for item_name, details in items.items():
            print(f"    Name: {item_name}")
            print(f"      Author: {details.get('author', 'N/A')}")
            print(f"      Download URL: {details.get('download', 'N/A')}")
            print("-" * 30)

def main():
    if len(sys.argv) < 2:
        print("Usage: mpreg <command> [arguments]                                             ")
        print("Commands:                                                                      ")
        print("  list-pkgs                            - List available packages               ")
        print("  list-misc                            - List available miscellaneous files    ")
        print("  download-pkg <package_name>          - Download a specific package           ")
        print("  download-misc <category> <item_name> - Download a specific miscellaneous file")
        sys.exit(1)

    command = sys.argv[1]

    if command == "list-pkgs":
        pkg_data = fetch_json_data(PACKAGE_LIST_URL)
        list_packages(pkg_data)
    elif command == "list-misc":
        misc_data = fetch_json_data(MISC_LIST_URL)
        list_misc_files(misc_data)
    elif command == "download-pkg":
        if len(sys.argv) < 3:
            print("Usage: mpreg.py download-pkg <package_name>", file=sys.stderr)
            sys.exit(1)
        package_name = sys.argv[2]
        pkg_data = fetch_json_data(PACKAGE_LIST_URL)
        if pkg_data and package_name in pkg_data:
            download_url = pkg_data[package_name].get("download")
            if download_url and download_url != "soon":
                print(f"Attempting to download package: {package_name}")
                download_file(download_url, destination_folder="downloads/pkgs", filename=package_name)
            else:
                if not download_url:
                    print(f"Download URL for package '{package_name}' is not present or blank in the package list.", file=sys.stderr)
                elif download_url == "soon":
                    print(f"Download URL for package '{package_name}' is coming soon.", file=sys.stderr)
        else:
            if not pkg_data:
                print(f"Package list is either empty or failed to download.", file=sys.stderr)
            elif not package_name in pkg_data:
                print(f"Package '{package_name}' not found.", file=sys.stderr)
    elif command == "download-misc":
        if len(sys.argv) < 4:
            print("Usage: python3 mpreg.py download-misc <category> <item_name>", file=sys.stderr)
            sys.exit(1)
        category = sys.argv[2]
        item_name = " ".join(sys.argv[3:])

        misc_data = fetch_json_data(MISC_LIST_URL)
        if misc_data and category in misc_data and item_name in misc_data[category]:
            download_url = misc_data[category][item_name].get("download")
            if download_url and download_url != "soon":
                print(f"Attempting to download miscellaneous file: {item_name} from category: {category}")
                download_file(download_url, destination_folder=f"downloads/misc/{category}", filename=os.path.basename(download_url))
            else:
                if not download_url:
                    print(f"Download URL for miscellaneous item '{item_name}' in category '{category}' is not present or is blank in the miscellaneous files list.", file=sys.stderr)
                elif if download_url == "soon":
                    print(f"Download URL for miscellaneous item '{item_name}' in category '{category}' is coming soon.")
        else:
            if not misc_data:
                print(f"Miscellaneous files list is either empty or failed to download.", file=sys.stderr)
            elif not category in misc_data:
                print(f"Miscellaneous category '{category}' not found.", file=sys.stderr)
            elif not item_name in misc_data[category]:
                print(f"Miscellaneous item '{item_name}' in category '{category}' not found.", file=sys.stderr)
    else:
        print(f"Unknown command: {command}", file=sys.stderr)
        print("Run 'mpreg' without any arguments for usage details.", file=sys.stderr)
        sys.exit(1)

if __name__ == "__main__":
    main()
