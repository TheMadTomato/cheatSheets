# All You Need To Know about archiving and compression

**credits goes to this StackOverflow post: https://unix.stackexchange.com/questions/731609/difference-between-ar-tar-gzip-zip-and-when-should-i-decide-to-choose-which-o**

> I had a use case where I needed to pack a bunch of files into one

Ah, you need an archive of files

> And all above commands does the same.

Not at all! Some are archivers, some are compressors, some are decompressors, some a combination.

- *ar*: very archaic, use cases are very specific. Pretty certain you don't ever want to use *ar* yourself.
- *gzip* / *gunzip*: Not an archiver. Can take a single stream of data and compress it (or decompress it, in case of *gunzip*). You can use this together with an archiver. Gzip is very old and slow and inefficient, there's alternatives that achieve much higher compression or higher speed, or any mixture of that (e.g., *zstd*, *lz4*)
- *tar*: Short for tape archiver; a very common archiver that you can also tell to compress stuff. For example:
> tar cf archive.tar file1 file2 file3
creates an uncompressed archive containing file1, file2 and file3. However, adding the *z* option to the create command (I know, *tar*'s syntax is hellish):

> tar czf archive.tar.gz file1 file2 file3
will make *tar* use *gzip* internally and create a tar archive that's been compressed.
You can also just pipe the result through any compressor of your choice to get compressed archives, e.g.

> tar cf - file1 file2 file3 | gzip > archive.tar.gz # or
> tar cf - file1 file2 file3 | zstd > archive.tar.zst # or
> tar cf - file1 file2 file3 | lz4 - archive.tar.xz # or
> tar cf - file1 file2 file3 | xz > archive.tar.xz
You get the idea.
As common as *tar* is, it's a very old program and format(s), and it leaves a lot to be desired. But it does correctly deal with Linux file ownership, permissions, links, special files…

- *zip* is a compressing archiver. Works very nicely with windows, as well, but can not deal with file attributes such as owners and permissions. Hence, not usable for backups!
- *7z* is like zip, a compressing archiver, which cannot deal with user and permission information. Hence, not usable for backups!
- *mksquashfs* is a kind of an archiver, meant for very neatly packed archives, that can also be used like normal file systems. It can use modern, on request very fast or very strong compression.
> Now some would say you would save some time while transferring files on network using compression but unzipping and decompression compensates for the time that I would have saved in transfer.

And those people would be right! If you use a modern, speed-optimized compression, you'd be faster than reading or writing from an SSD with decompression. And much, much faster than your network would ever be (unless you are looking at datacenter-grade networking).

So, if speed is your concern, use something that makes use of a fast compressor. As said, *gzip* is probably not the compressor of choice in 2023, so

> tar cf - srces/ | zstd -1 > archive.tar.zst
achieves an archival rate of roughly 3 Gb/s (in case you planned to put this through network, and thought the compressor would be a bottleneck) in my test that uses a mixture of source code, binary files. It makes 1.4 GB out of the original 4.97 GB. Using *-2* instead of *-1* makes the result another 10% smaller, and reduces the speed to 2.5 Gb/s. Which is still faster than most SATA SSDs could write. And this was single-threaded. Use *zstd -2 -T0* to make use of all CPU cores, and my humble PC does 6.5 Gb/s; zstd -4 -T0 still does 2.5 Gb/s, so more than most of my network cards could do, and gets the size down to 1.2 GB :)

So:

- Need to archive files, but fast, for sending them to other people who might not have the same software as you? *tar cv - files… | zstd -4 -T0 > archive.tar.zst* is what you want
- Need to archive files, but strongly compressed, for sending them to other people who might not have the same software as you? *tar cv - files… | zstd -13 -T0 > archive.tar.zst* is slower, but gives very high compression ratios already.
- Need to archive files, want to read them later on, without having do un-archive things? *mksquashfs files… archive.squashfs -comp=zstd*; add *-Xcompression-level 4* to the end for higher speed at the expense of size.
The resulting *archive.tar.zst* files can be directly unarchived with modern GNU tar, *tar xf archive.tar.zst*; the archive.squashfs can either be mounted directly *udisksctl loop-setup -f archive.squashfs* and used like a DVD (i.e., you can directly browse the files on it), or de-archived using *unsquashfs archive.squashfs*
