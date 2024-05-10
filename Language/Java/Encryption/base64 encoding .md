## Base64 encoding
It converts binary data to a text format using 64 specific characters for encoding: A-Z, a-z, 0-9, +, and /. 
These characters represent values from 0 to 63. The conversion works by taking 3 bytes ( 24 bits) of binary data to 4 
Base64 characters (6 bits each). If the binary data cannot be perfectly grouped into groups of 3 bytes, it is *padded*
with = at the end. 

## Example
![base](https://github.com/brian6484/CSKnowledge/assets/56388433/361ad8fa-aff5-4125-9235-adc92e1e075f)

So lets say "Man". Each character needs to be changed to ASCII binary data according to that table above. Then it is 
split into groups of 24 bits. These are split to 4 Base64 characters of 6 bits each.

![base1](https://github.com/brian6484/CSKnowledge/assets/56388433/fa26cd75-3abb-4372-aeb6-6c2a4f03fbdf)

But problem is if perfect grouping of multiple of 3 bytes doesnt work

![base2](https://github.com/brian6484/CSKnowledge/assets/56388433/358d4c51-bb90-4b0b-9c6c-53e4ead4c0c2)
