+++
date = "2017-05-26"
draft = false
title = "Formats"
subtitle = "Specification of used text formats"
menu = "main"
weight = 3
+++

Following is the specification of the text formats used as inputs or outputs of the desktop application. What these formats have in common is that the encoding is plain ASCII (Unicode is not allowed) and the endlines are Unix-style (line feed only).

## Ground truth text format

This format is used when loading ground truth into the application (using the `--gt` command-line argument). The MATLAB code uses its own ground truth format in the form of binary `.mat` files; use [these MATLAB scripts](/files/ 	gt-txt-tools-2017-04-12.zip) to convert files from binary to text and back.

The file begins with the space-separated integers `W`, `H`, `F`, `O`, `L` on a separate line. These denote: width, height, number of frames, frame offset for playback, and the number of non-empty frames. In most cases, `O` is zero.

Following are `L` lines with the following format: integer `I`, `1 <= I <= F`, specifies the frame number that is being described on this line. Frame numbers use MATLAB's one-based indexing, i.e. the first frame is frame number 1. Integer `N`, `1 <= N < W*H`, specifies the length of run-length encoding of the frame.

The following `N` integers are lengths of runs of alternating color, starting with black. The image is considered to be a one-dimensional, contiguous array created by joining image rows together from top to bottom (this representation is commonplace in C and C++ graphics software). The length of the last run is omitted, and the importer needs to add the last run up to the number of pixels in the image.

Example file:
```
3 2 4 0 3
1 2 0 2
2 2 3 2
3 1 4
```

The file above encodes the following binary frames, where `.` is black and `X` is white:
```text
1    2    3    4
XX.  ...  ...  ...
...  XX.  .XX  ...

```

## Evaluation text format

This format is produced by the application using the `--eval-dir` command-line argument, and also read as input when via the `--baseline` argument.
        
Evaluation data starts with the string `/FMO/EVALUATION/V3/` on a separate line. Everything before this line is ignored. On the next line there is the integer `N`, specifying the number of sequences.

The following pattern of 6 lines is repeated `N` times. The first line contains a sequence name, the integer `F`, denoting the number of frames in the sequence, and the integer `O`, specifying the number of detected objects with non-zero IoU. The sequence name must be stripped of any directories or extensions (remove \_gt, .mat, .txt, .avi, .mp4, .mov, etc.) and spaces must be replaced with underscores. The next four lines contain information about FN, FP, TN, TP, respectively and strictly in this order. Each of these lines contains the name of the result (FN, FP, TN or TP) followed by `F` integers separated by spaces, specifying the number of the particular kind of result in each frame. The last line of the pattern contains the string `IOU`, and following are `O` integers in range 0 to 1000.

Example file:
```
/FMO/EVALUATION/V3/
2
example 5 4
FN 0 0 0 0 4
FP 0 0 2 0 0
TN 1 1 0 0 1
TP 0 0 0 1 0
IOU 512 799 14 501
example_2 2 1
FN 0 0
FP 0 0
TN 1 0
TP 0 1
IOU 698
```
