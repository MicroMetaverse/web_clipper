# Getting Started With the File System Access API | CSS-Tricks - CSS-Tricks
The File System Access API is a web API that allows read and write access to a user’s local files. It unlocks new capabilities to build powerful web applications, such as text editors or IDEs, image editing tools, improved import/export, all in the frontend. Let’s look into how to get started using this API.

![](https://i0.wp.com/css-tricks.com/wp-content/uploads/2022/02/file-system-prompt.png.webp?resize=1994%2C1262&ssl=1)

### Reading files with the File System Access API

Before diving into the code required to read a file from the user’s system, an important detail to keep in mind is that **calling the File System Access API needs to be done by a user gesture, in a secure context**. In the following example, we’ll use a click event.

#### Reading from a single file

Reading data from a file can be done in less than 10 lines of code. Here’s an example code sample:

    let fileHandle;
     
    document.querySelector(".pick-file").onclick = async () => {
     [fileHandle] = await window.showOpenFilePicker();
     
     const file = await fileHandle.getFile();
     const content = await file.text();
     
     return content;
    };

Let’s imagine we have a button in our HTML with the class `.pick-file`. When clicking on this button, we launch the file picker by calling `window.showOpenFilePicker()`, and we store the result from this query in a variable called `fileHandle`. 

What we get back from calling `showOpenFilePicker()` is an array of [`FileSystemFileHandle`](https://wicg.github.io/file-system-access/#api-filesystemfilehandle) objects representing each file we selected. As this example is for a single file, we destructure the result. I’ll show how to select multiple files a bit later.

These objects contain a `kind` and `name` property. If you were to use `console.log(fileHandle)`, you would see the following object:

    FileSystemFileHandle {kind: 'file', name: 'data.txt'}

The `kind` can either be `file` or `directory`.

On `fileHandle`, we can then call the `getFile()` method to get details about our file. Calling this method returns an object with a few properties, including a timestamp of when the file was last modified, the name of the file, its size, and type.

Finally, we can call `text()` on the file to get its content.

#### Reading from multiple files

To read from multiple files, we need to pass an `options` object to `showOpenFilePicker()`.

For example:

    let fileHandles;
    const options = {
     multiple: true,
    };
     
    document.querySelector(".pick-file").onclick = async () => {
     fileHandles = await window.showOpenFilePicker(options);
     
     
    };

By default, the `multiple` property is set to `false`. Other options can be used to indicate the types of files that can be selected.

For example, if we only wanted to accept `.jpeg` files, the options object would include the following:

    const options = {
     types: [
       {
         description: "Images",
         accept: {
           "image/jpeg": ".jpeg",
         },
       },
     ],
     excludeAcceptAllOption: true,
    };

In this example, `fileHandles` is an array containing multiple files, so getting their content would be done in the following way:

    let fileHandles;
    const options = {
     multiple: true,
    };
     
    document.querySelector(".pick-file").onclick = async () => {
     fileHandles = await window.showOpenFilePicker(options);
     
     const allContent = await Promise.all(
       fileHandles.map(async (fileHandle) => {
         const file = await fileHandle.getFile();
         const content = await file.text();
         return content;
       })
     );
     
     console.log(allContent);
    };

### Writing to a file with the File System Access API

The File System Access API also allows you to write content to files. First, let’s look into how to save a new file.

#### Writing to a new file

Writing to a new file can also be done in a very short amount of code!

    document.querySelector(".save-file").onclick = async () => {
     const options = {
       types: [
         {
           description: "Test files",
           accept: {
             "text/plain": [".txt"],
           },
         },
       ],
     };
     
     const handle = await window.showSaveFilePicker(options);
     const writable = await handle.createWritable();
     
     await writable.write("Hello World");
     await writable.close();
     
     return handle;
    };

If we imagine a second button with the class `save-file`, on click, we open the file picker with the method `showSaveFilePicker()` and we pass in an `option` object containing the type of file to be saved, here a `.txt` file.

Calling this method will also return a `FileSystemFileHandle` object like in the first section. On this object, we can call the `createWritable()` method that will return a `FileSystemWritableFileStream` object. We can then write some content to this stream with the `write()` method in which we need to pass the content.

Finally, we need to call the `close()` method to close the file and finish writing the content to disk.

If you wanted to write some HTML code to a file for example, you would only need to change what’s in the `options` object to accept `"text/html": [".html"]`  and pass some HTML content to the `write()` method.

#### Editing an existing file

If you’d like to import a file and edit it with the File System Access API,  an example code sample would look like:

    let fileHandle;
     
    document.querySelector(".pick-file").onclick = async () => {
     [fileHandle] = await window.showOpenFilePicker();
     
     const file = await fileHandle.getFile();
     const writable = await fileHandle.createWritable();
     
     await writable.write("This is a new line");
     await writable.close();
    };

If you’ve been following the rest of this post, you might recognize that we start with the `showOpenFilePicker()` and `getFile()` methods to read a file and we then use `createWritable()`, `write()` and `close()` to write to that same file.

If the file you’re importing already has content, this code sample will replace the current content with the new one passed into the `write()` method.

### Additional File System Access API features

Without going into too much detail, the File System Access API also lets you list files in directories and delete files or directories.

#### Read directories

Reading directories can be done with a tiny bit of code:

    document.querySelector(".read-dir").onclick = async () => {
     const directoryHandle = await window.showDirectoryPicker();
     
     for await (const entry of directoryHandle.values()) {
       console.log(entry.kind, entry.name);
     }
    };

If we add a new button with the class `.read-dir`, on click, calling the `showDirectoryPicker()` method will open the file picker and, when selecting a directory on your computer, this code will list the files found in that directory.

#### Delete files

Deleting a file in a directory can be done with the following code sample:

    document.querySelector(".pick-file").onclick = async () => {
     const [fileHandle] = await window.showOpenFilePicker();
     await fileHandle.remove();
    };

If you want to delete a folder, you only need to make a small change to the code sample above:

    document.querySelector(".read-dir").onclick = async () => {
     const directoryHandle = await window.showDirectoryPicker();
     await directoryHandle.remove();
    };

Finally, if you want to remove a specific file when selecting a folder, you could write it like this:

     document.querySelector(".pick-folder").onclick = async () => {
       const directoryHandle = await window.showDirectoryPicker();
       await directoryHandle.removeEntry("data.txt");
    };

And if you want to remove an entire folder, you would need the following lines:

     document.querySelector(".pick-folder").onclick = async () => {
       const directoryHandle = await window.showDirectoryPicker();
       await directoryHandle.removeEntry('data', { recursive: true });
    };

### File System Access API browser support

At the moment, IE and Firefox don’t seem to be supporting the File System Access API. However, there exists a [ponyfill](https://github.com/sindresorhus/ponyfill) called [browser-fs-access](https://github.com/GoogleChromeLabs/browser-fs-access).

#### Desktop

| Chrome | Firefox | IE  | Edge | Safari |
| ------ | ------- | --- | ---- | ------ |
| 103    | No      | No  | 99   | TP     |

#### Mobile / Tablet

| Android Chrome | Android Firefox | Android | iOS Safari |
| -------------- | --------------- | ------- | ---------- |
| No             | No              | No      | 15.4       |

### Wrapping up

If you’d like to try the File System Access API, check out this [live demo text editor](https://googlechromelabs.github.io/text-editor/) built by Google engineers. Otherwise, if you’d like to learn more about this API and all its features, here are some resources:

-   [File System Access API](https://wicg.github.io/file-system-access/) (W3C Specification)
-   [File System Access API](https://developer.mozilla.org/en-US/docs/Web/API/File_System_Access_API) (MDN)
-   [Contrast Ratio Range, replaceAll Method, Native File System API](https://css-tricks.com/weekly-platform-news-contrast-ratio-range-replaceall-method-native-file-system-api/#aa-try-out-the-native-file-system-api-in-chrome) (Šime Vidas)
-   [The File System Access API: simplifying access to local files](https://web.dev/file-system-access/#deleting-files-and-folders-in-a-directory) (web.dev)
-   [Reading and writing files and directories with the browser-fs-access library](https://web.dev/browser-fs-access/) (web.dev)
-   [browser-fs-access repo](https://github.com/GoogleChromeLabs/browser-fs-access) (GitHub) 
    [https://css-tricks.com/getting-started-with-the-file-system-access-api/](https://css-tricks.com/getting-started-with-the-file-system-access-api/)
