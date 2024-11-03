# Digital Forensics Fundamentals

## [Task 1] Introduction to Digital Forensics

Forensics is the use of scientific methods and procedures to solve crimes. When investigating cyber crime, we call this _digital forensics_. Cyber crime is simply any criminal activity conducted on or using a digital device. Tools and techniques exist to investigate digital devices after a crime occurs and find/analyze evidence for legal action.

Digital devices have made things much easier, and this unfortunately includes crimes. The room provides an example of law enforcement raiding and collecting the devices of a bank robber. The case gets handed over to the digital forensics team, which securely collects evidence and investigates the digital forensics lab with tools. The evidence they found includes a map of the bank, a document with routes through the bank, a list of the physical security controls used by the bank, media files of previous robberies, and chat groups/call records.

Evidence like this can be used in legal proceedings. We'll be focusing on the procedures used to collect, store, analyze, and report on evidence for this purpose.

**[Task 1, Question 1] Which team was handed the case by law enforcement?** - Digital forensics

## [Task 2] Digital Forensics Methodology

Digital forensics requires different tools and techniques. The National Institute of Standards and Technology, NIST, provides a process for digital forensics cases. NIST handles a lot of things like this, actually - they're responsible for deifning many frameworks for technology, including cybersecurity. Here's what they say about the digital forensics process:
1. Collection: Identifying devices from which the data can be collected, ensuring the original data isn't tampered with, documenting details of the collected items.
2. Examination: Filtering data so that you can only focus on the data of interest
3. Analysis: A critical phase in which investigators have to correlate data with other pieces of evidence to draw conclusions. The process here will depend on the case and avaialble data. The goal is to extract activities relevant to the case chronologically.
4. Reporting: A detailed report is prepared, summarizing the methodology and providing detailed findings from the collected evidence, along with recommendations as appropriate. This is given to law enforcement and executive management.

Various pieces of evidence can be found at the crime scene, and each type may require different tools and techniques. There are many different types of digital forensics as a result:
- Computer forensics: The most common type, focusing on computers
- Mobile forensics: Focusing on mobile devices, call records, text messages, GPS locations, etc.
- Network forensics: Focusing on the entire network, including logs
- Database forensics: Critical data is stored in dedicated databases, so this involves investigating database intrusions
- Cloud forensics: Investigating data stored on cloud infrastrucure, which can be tricky to do
- Email forensics: The most common communication method, investigating what emails were sent and what they were used for

**[Task 2, Question 1] Which phase of digital forensics is concerned with correlating the collected data to draw any conclusions from it?** - Analysis

**[Task 2, Question 2] Which phase of digital forensics is concerned with extracting the data of interest from the collected evidence?** - Examination

## [Task 3] Evidence Acquisition

In forensics, acquiring evidence is a critical job. The data must be collected securely without tampering with the original data. Evidence acquisition methods for digital devices depend on the type of digital device. There are some best practices that need to be followed, however.

In collecting evidence, you should obtain proper authorization from the relevant authorities before collecting data. Without doing so, the collected evidence may may be deemed inadmissible in court. After all, forensic evidence contains private and sensitive data of an organization or individual.

We also need to document the owners of the evidence. In the event that evidence goes missing or is changed, we need to be able to identify who was involved. We do this with a chain of custody document, which contains all details of the evidence. Some key details may include
- The description of the evidence (name, type)
- Name of individuals who collected the evidence
- Date and time of evidence collection
- Storage location of each piece of evidence
- Access times and the individual record of who acessed the evidence

Chain of custody documents create a proper trail of evidence and help preserve it. This can be used to prove the integrity and reliability of evidence admitted in court.

Write blockers are also often used in digital forensics. Background tasks in the forensic workstation - where you hook up the drives you want to analyze - may cause data to be changed in the files of the hard drive, such as timestamps. This can hinder analysis and produce incorrect results. Write blockers prevent these tasks from actually making changes, thus keeping the hard drive in its original state.

**[Task 3, Question 1] Which tool is used to ensure data integrity during the collection?** - write blocker

**[Task 3, Question 2] What is the name of the document that has all the details of the collected digital evidence?** - chain of custody

## [Task 4] Windows Forensics

Digital evidence commonly collected from a crime scene will typically come from computers and laptops; most criminal activity will involve a personal system. These devices have different operating systems running on them. We'll look at how data can be collected from WIndows.

When data collection begins, forensic images of the operating system are taken. These are bit-by-bit copies of the entire operating system. There are two types of forensic images that we may take:
- Disk images, which contain all the data present on the storage device of the system. It's non-volatile, so the data on the disk would survive even after a restart of the OS.
- Memory images, which contain all the data inside the operating system's RAM. This is volatile, so the data will get lost after the system is powered off or restarted. This should be prioritized and taken first.

Some tools used to collect disk and memory images include:
-  FTK Imager: This is used to take disk images of Windows operating systems via a user-friendly GUI. Images can be made in various formats. The contents of a disk image can also be analyzed.
-  Autopsy: A popular-open source platform for digital forensics. Images can be imported into the tool, which will conduct an extensive analysis of the image. This includes image analysis, keyword search, deleted file recovery, file metadata, extension mismatches, and more.
-  DumpIt: Used to take memory images from Windows. This is all done via the command-line interface and a few commands.
-  Volatility: A powerful open-source tool used for analyzing memory images. Comes with some useful plugins that can be used to analyze specific artifacts. Suppots various operating systems.

Other tools can be used to acquire and analyze disk and memory images, of course.

**[Task 4, Question 1] Which type of forensic image is taken to collect the volatile data from the operating system?** - memory image

## [Task 5] Practical Example of Digital Forensics

Let's do some digital forensics. Say someone has kidnapped our cat, and sent us a document with their requests in a Microsoft Word document. The document has been converted to a PDF, and the image has been extract from the Word file. The files associated with this task can be downloaded from the room or accessed from the AttackBox at `/root/Rooms/introdigitalforensics`.

When you create a text file, some metadata will get saved by the operating system, such as the file creation and last modification dates. More information gets saved when creating a Word document, which is made with a more advanced editor. There are many ways to read the file metadata. An official viewer/editor can be used, as well as a suitable forensic tool. Exporting the file to other formats would maintain the metadata from the original document, depending on the PDF writer used.

We can use `pdfinfo` to learn more about the PDF file's metadata. This includes the title, subject, author, creator, and creation date. This has been installed on the AttackBox, but if it's not installed, you can grab it from `poppler-utils`. In Kali Linux, this can be installed with `sudo apt install poppler-utils`. To use the command, run `pdfinfo (PDF_FILE)`.

Photo EXIF data can also be investigated - EXIF standing for Exchangeable Image File Format. This is a standard for saving metadata to image files. When a photo is taken on your smartphone or a digital camera, information is embedded into the image, such as the camera/smartphone model, the date/time of image capture, and photo settings such as local length, aperture, shutter speed, and ISO settings. You are also likely to find GPS coordinates in the metadata, giving you an idea of where the photo was originally taken.

The `exiftool` command can be used to read and write metadaata in file types, including JPEG images. This is already set up in the AttackBox, but it can be installed in Kali Linux with `sudo apt install libimage-exiftool-perl` if not already installed. To use the command, run `exiftool (IMAGE_FILE)`. If you find coordinates, you can use Google Maps or something similar to find the physical location. Just swap out `deg` with `°` and remove the extra whitespaces in the longitude and latitude, as well as the commas.

Let's start by using `pdfinfo` on the ransom letter. Running `pdfinfo ransom-letter.pdf` gives us the following information:

![image](https://github.com/user-attachments/assets/714b7b1e-d263-45f7-a588-2d748d4f694b)

The author is Ann Gree Shepherd.

**[Task 5, Question 1] Using `pdfinfo`, find out the author of the attached PDF file, `ransom-letter.pdf`.** - Ann Gree Shepherd

Now let's investigate the image with `exiftool letter-image.jpg`. The coordinates are given as follows:

![image](https://github.com/user-attachments/assets/4e2dc8e5-7d12-44b7-8e57-40ce87a7161f)

If we look up `51°30'51.9"N 0°05'38.7"W`, we get:

![image](https://github.com/user-attachments/assets/2bb51d90-ded2-4835-b672-e44baff8d54a)

**[Task 5, Question 2] Using `exiftool` or any similar tool, try to find where the kidnappers took the image they attached to their documents. What is the name of the street?** - Milk Street

The camera model can be found further up in the `exiftool` results, though we could use `grep` to find it easily:

![image](https://github.com/user-attachments/assets/3c22d83d-3ee2-4807-955e-e586da401aab)

**[Task 5, Question 3] What is the model name of the camera used to take this photo?** - Canon EOS R6
