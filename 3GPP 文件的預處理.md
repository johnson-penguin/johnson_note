---
title: 3GPP 文件的預處理

---

## 簡介：3GPP 文件的平行文件處理

自動化和最佳化文件檔案的處理，特別是針對 3GPP 文件集合。它採用兩步驟並行處理方法來有效地處理大量文件。該腳本在指定的基本目錄內運行，以「3GPP-all」資料夾為目標，並處理在其子目錄中找到的文件。

主要特點：
1. `file_exists`：驗證檔案是否存在，確保有效率處理檔案操作。
2. `unzip_task_directory`：自動解壓縮`3GPP-all`目錄中的檔案，並檢查以避免對已解壓縮的檔案進行不必要的處理。
3. 有系統地遍歷嵌套目錄結構，辨識並準備要處理的檔案。
4. 實作`ThreadPoolExecutor`進行並行處理，大幅提升解壓縮、文件轉換任務的效率。
5. 創新使用多個 LibreOffice 實例進行並行

```python=
import os
import zipfile
import subprocess
from concurrent.futures import ThreadPoolExecutor, TimeoutError

def file_exists(file_path):
    return os.path.exists(file_path)

def unzip_task_directory(directory):
    print(f"Unzipping files in {directory}...")
    for item in os.listdir(directory):
        if item.endswith('.zip'):
            file_path = os.path.join(directory, item)
            extract_path = file_path.replace('.zip', '')

            
            if not file_exists(extract_path):
                try:
                    with zipfile.ZipFile(file_path, 'r') as zip_ref:
                        zip_ref.extractall(directory)
                    print(f"  Unzipped {item}")
                    os.remove(file_path)
                    print(f"  Deleted {item}")
                except zipfile.BadZipFile:
                    print(f"  Warning: {file_path} is not a valid zip file and will be skipped.")
            else:
                print(f"  Skipping unzipping {item}, already exists.")


base_directory = "3GPP_test"
base_directory = os.path.abspath(base_directory)
directories_to_process = []

# Collect directories for unzipping
for release_dir in os.listdir(base_directory):
    release_path = os.path.join(base_directory, release_dir)
    if os.path.isdir(release_path):
        for version_dir in os.listdir(release_path):
            version_path = os.path.join(release_path, version_dir)
            if os.path.isdir(version_path):
                directories_to_process.append(version_path)
```

## Unzip files
```python=
# First ThreadPoolExecutor for unzipping
with ThreadPoolExecutor(max_workers=10) as executor:
    executor.map(unzip_task_directory, directories_to_process)
```

## Use Libreoffice to convert doc files to docx and the to markdown in parallel
```python=

import os
import subprocess
import time
failed_files = []
failure_count = 0

def file_exists(file_path):
    return os.path.exists(file_path)


def convert_doc_to_docx_and_markdown(doc_path):
    directory = os.path.dirname(doc_path)
    docx_path = doc_path + 'x'
    markdown_file = os.path.splitext(docx_path)[0] + '.md'

    # Ensure a temp directory exists in the current working directory
    temp_dir = os.path.join(os.getcwd(), 'temp')
    os.makedirs(temp_dir, exist_ok=True)

    # Generate a unique identifier for the LibreOffice user profile
    unique_id = str(time.time()).replace('.', '')

    # Create a LibreOffice user profile directory inside the temp folder
    temp_libreoffice_dir = os.path.join(temp_dir, f"libreoffice_temp_{unique_id}")
    os.makedirs(temp_libreoffice_dir, exist_ok=True)
    user_installation_path = f"file://{temp_libreoffice_dir}"

    # Convert DOC to DOCX
    if not file_exists(docx_path):
        try:
            subprocess.run([
                "libreoffice", 
                "-env:UserInstallation=" + user_installation_path,
                "--headless", 
                "--convert-to", "docx", 
                doc_path, 
                "--outdir", directory], 
                check=True, 
                stderr=subprocess.PIPE)
            print(f"    Converted {os.path.basename(doc_path)} to .docx")
        except subprocess.CalledProcessError as e:
            print(f"    Error converting {os.path.basename(doc_path)} to .docx: {e}")
            print(f"    LibreOffice error: {e.stderr.decode()}")

    # Check if DOCX file exists before converting to Markdown
    if file_exists(docx_path):
        if not file_exists(markdown_file):
            try:
                subprocess.run(['pandoc', '-s', docx_path, '-o', markdown_file], check=True)
                print(f"    Converted {os.path.basename(docx_path)} to Markdown")
            except subprocess.CalledProcessError as e:
                print(f"    Error converting {os.path.basename(docx_path)} to Markdown: {e}")
    else:
        print(f"    {docx_path} does not exist. Skipping Markdown conversion.")

```