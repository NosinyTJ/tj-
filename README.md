#!/usr/bin/env python3
"""
organize_folder.py
Sorts files in a target directory into subfolders by file type.

Usage:
    python organize_folder.py /path/to/folder
    python organize_folder.py            # defaults to current directory
"""

import sys
import shutil
from pathlib import Path

FILE_CATEGORIES = {
    "Images": [".jpg", ".jpeg", ".png", ".gif", ".webp", ".svg", ".bmp"],
    "Documents": [".pdf", ".docx", ".doc", ".txt", ".md", ".rtf", ".odt"],
    "Spreadsheets": [".xlsx", ".xls", ".csv"],
    "Presentations": [".pptx", ".ppt"],
    "Audio": [".mp3", ".wav", ".flac", ".aac", ".m4a"],
    "Video": [".mp4", ".mov", ".avi", ".mkv", ".webm"],
    "Archives": [".zip", ".rar", ".7z", ".tar", ".gz"],
    "Code": [".py", ".js", ".html", ".css", ".json", ".java", ".cpp", ".c"],
    "Installers": [".exe", ".dmg", ".pkg", ".msi"],
}

def get_category(extension):
    for category, extensions in FILE_CATEGORIES.items():
        if extension.lower() in extensions:
            return category
    return "Other"

def organize_folder(target_dir):
    target = Path(target_dir).expanduser().resolve()

    if not target.is_dir():
        print(f"Error: '{target}' is not a valid directory.")
        sys.exit(1)

    moved_count = 0

    for item in target.iterdir():
        if item.is_file():
            category = get_category(item.suffix)
            category_folder = target / category
            category_folder.mkdir(exist_ok=True)

            destination = category_folder / item.name

            # Avoid overwriting files with the same name
            counter = 1
            while destination.exists():
                destination = category_folder / f"{item.stem}_{counter}{item.suffix}"
                counter += 1

            shutil.move(str(item), str(destination))
            print(f"Moved: {item.name} -> {category}/")
            moved_count += 1

    print(f"\nDone. {moved_count} file(s) organized in '{target}'.")

if __name__ == "__main__":
    folder = sys.argv[1] if len(sys.argv) > 1 else "."
    organize_folder(folder)
python organize_folder.py ~/Downloads
