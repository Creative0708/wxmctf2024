Writeup(s) by Justin
For context, Forensics is the only thing I actually know how to do lol
### Crypto 1: Detective Pikachu!
Running `cat` on `I_love_pokemon.txt` produces what looks like a bunch of empty lines, however, opening it in a text editor like Kate or something shows a lot of spaces and different indentation - the whitespace language. Once fed to a [whitespace interpreter](https://www.dcode.fr/whitespace-language), the output is 
```
pi pi pi pi pi pi pi pi pi pi pika pipi pi pipi pi pi pi pipi pi pi pi pi pi pi pi pipi pi pi pi pi pi pi pi pi pi pi pichu pichu pichu pichu ka chu pipi pipi pipi pipi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pikachu pi pikachu ka ka ka ka ka ka ka ka ka ka ka pikachu ka ka ka ka ka ka ka ka ka ka pikachu pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pikachu ka ka ka ka ka ka ka ka ka ka ka ka ka ka pikachu pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pikachu ka ka ka ka ka ka ka ka ka ka ka pikachu ka ka ka ka ka ka ka pikachu pi pi pikachu ka ka ka ka ka ka ka ka ka ka pikachu ka ka pikachu pi pi pi pi pikachu pi pi pi pi pi pikachu ka ka ka pikachu pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pikachu pi pi pikachu pi pi pi pi pikachu pichu pichu pichu pikachui pi pi pi pi pi pi pi pi pi pika pipi pi pipi pi pi pi pipi pi pi pi pi pi pi pi pipi pi pi pi pi pi pi pi pi pi pi pichu pichu pichu pichu ka chu pipi pipi pipi pipi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pikachu pi pikachu ka ka ka ka ka ka ka ka ka ka ka pikachu ka ka ka ka ka ka ka ka ka ka pikachu pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pikachu ka ka ka ka ka ka ka ka ka ka ka ka ka ka pikachu pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pikachu ka ka ka ka ka ka ka ka ka ka ka pikachu ka ka ka ka ka ka ka pikachu pi pi pikachu ka ka ka ka ka ka ka ka ka ka pikachu ka ka pikachu pi pi pi pi pikachu pi pi pi pi pi pikachu ka ka ka pikachu pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pi pikachu pi pi pikachu pi pi pi pi pikachu pichu pichu pichu pikachu
```
which admittedly had me confused... for about 3 seconds, after which I googled "pika language interpreter" and found [this](https://www.dcode.fr/pikalang-language), which spit out the flag: `wxmctf{pika_chewy}`.
### Forensics 2: Covert Chinchillas
The first thing that should be done when doing forensics problems with images, the most obvious tools that should be used are [exiftool](https://exiftool.org/) and [steghide](https://steghide.sourceforge.net/)/[stegseek](https://github.com/RickdeJager/stegseek). In the case of this problem, the correct first step is to view the metadata, as the comment holds a base64 encoded string: 
```
$ exiftool -s chinchilla.jpeg

[...]

Comment                         : cGFzc3dvcmQ6IDV1cDMyXzUzYzIzN181bjM0a3lfcDQ1NXcwMmQ=

[...]
```

Running `base64 -d` or `base64 --decode` using the string yields 
```
password: 5up32_53c237_5n34ky_p455w02d
```

From here, use `stegseek` with the wordlist option with the password in a text file:
```
$ stegseek chinchilla.jpeg wordlist.txt
```
(The contents of wordlist.txt should contain `5up32_53c237_5n34ky_p455w02d`)

The flag is then located in `chinchilla.jpeg.out`.

### Forensics 3: 2^3
So while I was presumably listening to an old chinese fellow talk about gravitation or something, Phil was hard at work writing some extreme python code to get the RGB values of the top left pixels as they were a seemingly random colors. The RGB values were then converted to binary, then sorted into an array
```
{"00000000", "11111111", "10000000", "11111111", "01111111", "11111111", "11111111", "11111111", "00000000", "11111000", "11001111", "11100111", "10000000", "01111110", "00011001", "00000111", "00000000", "11101101", "10011011", "01010010", "10101101", "11000000", "01000010", "11001111", "00000000", "10111110", "11010011", "01111111", "00000101", "00011010", "10101010", "10010001", "00000000", "10010001", "10111010", "10111010", "00000001", "10000000", "00111010", "00101010", "00000000", "10000011", "10100001", "00100001", "00000010", "10000011", "10100010", "00100000", "00000000", "10000000", "10000001", "10000000", "10000000", "00000000", "10000000", "10000001", "00000000", "00000001", "10000001", "10000001", "00000001", "00000001", "00000000", "00000001", "00000000", "10000000", "10000000", "00000000", "10000000", "10000000", "10000000", "00000000", "00000000", "00000000", "10000000", "10000000", "00000000", "00000000", "10000000", "10000000", "00000000", "00000000", "00000000", "00000000", "00000000", "00000000", "00000000", "00000000", "00000000", "00000000"}
```
After giving up and doing a crypto chall, he sent the bytes to me. Obviously, it would be to easy if concatenating all of those bytes gave the flag (but I tried it anyways) so I moved on to extracting each nth bit from each byte (ie. all of the first bits, all of the second bits, etc.) Realizing that this would take ages to do by hand, I leveraged my ICS3U knowledge and cobbled together this steaming piece of s***:
```java
public class Main {
	public static void main(String[] args) {
		String[] bytes = {"00000000", "11111111", "10000000", "11111111", "01111111", "11111111", "11111111", "11111111", "00000000", "11111000", "11001111", "11100111", "10000000", "01111110", "00011001", "00000111", "00000000", "11101101", "10011011", "01010010", "10101101", "11000000", "01000010", "11001111", "00000000", "10111110", "11010011", "01111111", "00000101", "00011010", "10101010", "10010001", "00000000", "10010001", "10111010", "10111010", "00000001", "10000000", "00111010", "00101010", "00000000", "10000011", "10100001", "00100001", "00000010", "10000011", "10100010", "00100000", "00000000", "10000000", "10000001", "10000000", "10000000", "00000000", "10000000", "10000001", "00000000", "00000001", "10000001", "10000001", "00000001", "00000001", "00000000", "00000001", "00000000", "10000000", "10000000", "00000000", "10000000", "10000000", "10000000", "00000000", "00000000", "00000000", "10000000", "10000000", "00000000", "00000000", "10000000", "10000000", "00000000", "00000000", "00000000", "00000000", "00000000", "00000000", "00000000", "00000000", "00000000", "00000000"};

		for(int i = 0; i < 8; i++) {
			for(String s : bytes) {
				System.out.print(s.charAt(i));
			}
		System.out.println();
		}
	}
}
```
Sure, you can probably do much more efficiently with a node or python script, but who do I look like, Colin?

---
Hi, Colin here. Indeed it can be done with Javascript:
```js
> Array(8).fill(0).flatMap((_,i)=>["00000000", "11111111", "10000000", "11111111", "01111111", "11111111", "11111111", "11111111", "00000000", "11111000", "11001111", "11100111", "10000000", "01111110", "00011001", "00000111", "00000000", "11101101", "10011011", "01010010", "10101101", "11000000", "01000010", "11001111", "00000000", "10111110", "11010011", "01111111", "00000101", "00011010", "10101010", "10010001", "00000000", "10010001", "10111010", "10111010", "00000001", "10000000", "00111010", "00101010", "00000000", "10000011", "10100001", "00100001", "00000010", "10000011", "10100010", "00100000", "00000000", "10000000", "10000001", "10000000", "10000000", "00000000", "10000000", "10000001", "00000000", "00000001", "10000001", "10000001", "00000001", "00000001", "00000000", "00000001", "00000000", "10000000", "10000000", "00000000", "10000000", "10000000", "10000000", "00000000", "00000000", "00000000", "10000000", "10000000", "00000000", "00000000", "10000000", "10000000", "00000000", "00000000", "00000000", "00000000", "00000000", "00000000", "00000000", "00000000", "00000000", "00000000"].map(x=>x[i]).join``.match(/.{8}/g).flatMap(x=>+x?String.fromCharCode(parseInt(x,2)):[])).join``
'wxmctf{0n3_tW0_THR33_F0ur_fiV3_5IX_53v3N_3i9Ht!}'
```
Back to Justin you go.

---

After putting each each individual line into a binary to ASCII converter (for some reason pasting the entire thing doesn't work), the flag is obtained.

### Miscellaneous 3: First Grep

The solution is:
```
unzip firstgrep.zip
cd firstgrep
grep -R wxmctf{
```

This gets you the flag in just 3 steps.


And that's about it from me. Go read the other writeups for more impressive achievements.