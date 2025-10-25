## hard link
it directly points to a file's inode and since it knows the file's inode, even if the original file is removed via `rm`, it doesnt delete the data. The data is only truly removed when the last remaining hard link is deleted.
the original file and hard link all share the same inode! so thats y deleting original file still retains the original data that is accessible via this hardlink.

## symbolic link
it points to the file's name/path. Original and symbolic have different, unique inodes so if original file is deleted, this symbolic link breaks and points to a **non-existent path**.

Let's use a file named **`original.txt`** located in the directory `/home/user/files/`.

***

## 1. Hard Link Example

| Action | Command | Result & Explanation |
| :--- | :--- | :--- |
| **Original File** | `ls -i /home/user/files/original.txt` | Shows: **`1000 original.txt`** (Inode **1000**) |
| **Create Hard Link** | `ln original.txt hardlink.txt` | A new directory entry is created, also pointing to **Inode 1000**. |
| **Verify Inode** | `ls -i original.txt hardlink.txt` | Shows: **`1000 original.txt`** and **`1000 hardlink.txt`** |
| **Delete Original** | `rm original.txt` | The directory entry for `original.txt` is removed, but the data on disk **remains available** because `hardlink.txt` is still pointing to **Inode 1000**. |
| **Access Link** | `cat hardlink.txt` | The content is **still accessible** because the file data structure (Inode 1000) was never deleted. |

**Summary:** Hard links are truly just another name for the data; deleting one link doesn't delete the file until the very last link is gone. 

***

## 2. Symbolic Link Example (Soft Link)

| Action | Command | Result & Explanation |
| :--- | :--- | :--- |
| **Original File** | `ls -i /home/user/files/original.txt` | Shows: **`1000 original.txt`** (Inode **1000**) |
| **Create Soft Link** | `ln -s original.txt softlink.txt` | A *new file* is created that holds the text string: `"original.txt"`. |
| **Verify Inode** | `ls -i original.txt softlink.txt` | Shows: **`1000 original.txt`** and **`1001 softlink.txt`** (The soft link has a **different** Inode). |
| **Delete Original** | `rm original.txt` | The file data (Inode 1000) is deleted. `softlink.txt` (Inode 1001) still exists, but it points to a file name that is now gone. |
| **Access Link** | `cat softlink.txt` | The system throws an error: **"No such file or directory."** The link is broken (dangling). |

**Summary:** The symbolic link is a pointer to the *name* of the file. If the name or path disappears, the link stops working, just like a shortcut on your desktop breaks if you move the program's executable. 
