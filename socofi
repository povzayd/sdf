#!/bin/bash

# Define colors
GREEN="\e[32m"   # 200 - OK
YELLOW="\e[33m"  # 301/302 - Redirects
RED="\e[31m"     # 403 - Forbidden
MAGENTA="\e[35m" # 401 - Unauthorized
GRAY="\e[90m"    # 500+ - Server errors
BLUE="\e[34m"    # Banner
RESET="\e[0m"    # Reset color

# Banner
echo -e "${BLUE}Exposed Directory Scanner by @YourName${RESET}"
echo "---------------------------------------------------"

# Check if file is provided
if [ -z "$1" ]; then
    echo -e "${RED}Usage: $0 <subdomains.txt>${RESET}"
    exit 1
fi

# Read subdomains
SUBDOMAINS=$(cat "$1")

# Directories to check
DIRS=(
    # Version Control & Backup
    "/.git/" "/.git/config" "/.svn/" "/.hg/" "/.bzr/" "/CVS/"
    "/backup/" "/bak/" "/old/" "/archive/" "/temp/" "/dev/" "/test/"
    "/.gitignore" "/.gitattributes" "/.gitmodules"
    "/.svn/entries" "/.hg/store/"
    "/.idea/" "/.vscode/" "/.settings/"
    
    # Framework & Language-Specific Paths
    "/vendor/" "/node_modules/" "/__pycache__/" "/WEB-INF/" "/META-INF/"
    "/config/" "/configs/" "/settings/" "/conf/" "/secrets/" "/env/"
    "/.env" "/.env.local" "/.env.production" "/.env.backup" "/.env.old"
    "/storage/" "/storage/logs/" "/tmp/" "/logs/" "/debug/" "/error_logs/"
    "/var/log/" "/logs/access.log" "/logs/error.log"
    "/src/" "/lib/" "/includes/" "/classes/" "/database/" "/db/"
    "/cgi-bin/" "/private/" "/public/" "/static/" "/uploads/"
    "/.htaccess" "/.htpasswd" "/robots.txt" "/sitemap.xml"
    "/phpinfo.php" "/wp-config.php" "/config.php" "/database.php"
    "/api/debug" "/api/v1/source" "/admin/debug" "/dev/api/"
    "/docker-compose.yml" "/Dockerfile" "/.dockerignore"
    "/.aws/credentials" "/.kube/config"
)

# Output file
OUTPUT="found_directories.txt"
echo -n "" > "$OUTPUT"

# Function to check a directory
check_directory() {
    domain=$1
    dir=$2
    url="http://$domain$dir"

    # Use curl with a timeout and silent mode
    response=$(curl -s --max-time 5 -o /dev/null -w "%{http_code}" "$url")

    # Assign color based on HTTP status
    case "$response" in
        200) COLOR=$GREEN ;;
        301|302) COLOR=$YELLOW ;;
        403) COLOR=$RED ;;
        401) COLOR=$MAGENTA ;;
        5*) COLOR=$GRAY ;;
        *) return ;;  # Skip everything else (like 404)
    esac

    echo -e "${COLOR}[+] Found: $url (${response})${RESET}"
    echo "$url ($response)" >> "$OUTPUT"
}

# Scan each subdomain in parallel
for domain in $SUBDOMAINS; do
    echo -e "${BLUE}Scanning: $domain${RESET}"
    for dir in "${DIRS[@]}"; do
        check_directory "$domain" "$dir" &
    done
    wait  # Wait for all background tasks to finish before moving to the next domain
done

echo -e "${BLUE}Scan Complete! Results saved in $OUTPUT${RESET}"
