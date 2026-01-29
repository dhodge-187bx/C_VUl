## description
Out-of-Bounds Read and Functional Anomalies in janus-gateway Caused by Incorrect Buffer Boundary Calculation in RTP Header Extension Parsing Function
## affected version
1.3.3~1.0.0
## Deatil
This vulnerability exists in the `janus_rtp_header_extension_parse_abs_send_time` function of janus-gateway (in `src/rtp.c`), which is responsible for parsing the RTP Absolute Send Time header extension (extmap:4) field.
<img width="1220" height="394" alt="image" src="https://github.com/user-attachments/assets/de6f706e-483e-440d-b0cb-dd22fbe413f3" />

In the function, `buf` is the input original RTP packet buffer with a total valid length of `len`, and `ext` points to the start address of the target extension data inside `buf`. The correct number of remaining available bytes from the `ext` pointer to the end of the `buf` buffer should be calculated as `len - (ext - buf)` (the offset of `ext` relative to `buf` minus the total buffer length). However, the vulnerable code incorrectly calculates this value as `len - (ext - buf) - 1` by adding an unnecessary subtraction of `1`, and uses this wrong value as the upper limit for validating the extension data length `idlen`.

This incorrect calculation leads to two critical issues for janus-gateway v1.0.0 ~ v1.3.3:
