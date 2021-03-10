#!/bin/bash

# 64-bit rotate right function
# $1 = value to rotate
# $2 = magnitude of rotation
rotate()
{
	lshtmp=$(($(($1>>1))&$((0x7fffffffffffffff))))
	tmp=$(($lshtmp>>$(($2-1))))
	tmp=$(($(($(($1<<$((64-$2))))&$((0xffffffffffffffff))))|$tmp))

	# "return" $tmp
	echo $tmp
}


# Logical left shift: bash provides only an arithmetic left shift, which is not what we want for crypto
#this is an ugly hack, but it works and I couldn't think of another way to do this in pure bash
lshft()
{
	lshtmp=$(($(($1>>1))&$((0x7fffffffffffffff))))
	tmp=$(($lshtmp>>$(($2-1))))

	# "return" $tmp
	echo $tmp
}

#Compute and output the sha512 value of the first argument (a string, not a file)
sha512()
{
	input=$1

	input_hex=''
	for (( i=0; i < ${#input}; i++ ))
	do
		input_hex="$input_hex$(printf "%02x" "'${input:$i:1}")"
	done

	# Yay! sha512 in pure bash

	# pure bash = using only bash, no external applications. printf and echo are used, both of which are bash builtins

	# Initialize hash values
	#(first 64 bits of the fractional parts of the square roots of the first 8 primes 2..19):
	declare -a h
	h[0]=$((0x6a09e667f3bcc908))
	h[1]=$((0xbb67ae8584caa73b))
	h[2]=$((0x3c6ef372fe94f82b))
	h[3]=$((0xa54ff53a5f1d36f1))
	h[4]=$((0x510e527fade682d1))
	h[5]=$((0x9b05688c2b3e6c1f))
	h[6]=$((0x1f83d9abfb41bd6b))
	h[7]=$((0x5be0cd19137e2179))

	# Initialize array of round constants
	#(first 64 bits of the fractional parts of the cube roots of the first 80 primes 2..409):
	declare -a k
	k=(0x428a2f98d728ae22 0x7137449123ef65cd 0xb5c0fbcfec4d3b2f 0xe9b5dba58189dbbc 0x3956c25bf348b538 0x59f111f1b605d019 0x923f82a4af194f9b 0xab1c5ed5da6d8118 0xd807aa98a3030242 0x12835b0145706fbe 0x243185be4ee4b28c 0x550c7dc3d5ffb4e2 0x72be5d74f27b896f 0x80deb1fe3b1696b1 0x9bdc06a725c71235 0xc19bf174cf692694 0xe49b69c19ef14ad2 0xefbe4786384f25e3 0x0fc19dc68b8cd5b5 0x240ca1cc77ac9c65 0x2de92c6f592b0275 0x4a7484aa6ea6e483 0x5cb0a9dcbd41fbd4 0x76f988da831153b5 0x983e5152ee66dfab 0xa831c66d2db43210 0xb00327c898fb213f 0xbf597fc7beef0ee4 0xc6e00bf33da88fc2 0xd5a79147930aa725 0x06ca6351e003826f 0x142929670a0e6e70 0x27b70a8546d22ffc 0x2e1b21385c26c926 0x4d2c6dfc5ac42aed 0x53380d139d95b3df 0x650a73548baf63de 0x766a0abb3c77b2a8 0x81c2c92e47edaee6 0x92722c851482353b 0xa2bfe8a14cf10364 0xa81a664bbc423001 0xc24b8b70d0f89791 0xc76c51a30654be30 0xd192e819d6ef5218 0xd69906245565a910 0xf40e35855771202a 0x106aa07032bbd1b8 0x19a4c116b8d2d0c8 0x1e376c085141ab53 0x2748774cdf8eeb99 0x34b0bcb5e19b48a8 0x391c0cb3c5c95a63 0x4ed8aa4ae3418acb 0x5b9cca4f7763e373 0x682e6ff3d6b2b8a3 0x748f82ee5defb2fc 0x78a5636f43172f60 0x84c87814a1f0ab72 0x8cc702081a6439ec 0x90befffa23631e28 0xa4506cebde82bde9 0xbef9a3f7b2c67915 0xc67178f2e372532b 0xca273eceea26619c 0xd186b8c721c0c207 0xeada7dd6cde0eb1e 0xf57d4f7fee6ed178 0x06f067aa72176fba 0x0a637dc5a2c898a6 0x113f9804bef90dae 0x1b710b35131c471b 0x28db77f523047d84 0x32caab7b40c72493 0x3c9ebe0a15c9bebc 0x431d67c49c100d4c 0x4cc5d4becb3e42b6 0x597f299cfc657e2a 0x5fcb6fab3ad6faec 0x6c44198c4a475817)

	# Pad using MD-Compliant padding:
	input_len=${#input}
	input_len_bin=$(echo -e "\x"$(printf "%x" $(($input_len*8))))
	final_len=$(($(($(($input_len+128))>>7))<<7))

	pad_n=$(($final_len-$input_len))
	input_hex=$input_hex'80'
	for (( i=1; i < $(($pad_n-16)); i++ ))
	do
		input_hex=$input_hex'00'
	done
	input_hex=$input_hex$(printf "%032x" "$(($input_len<<3))")

	# This is the 80 word message schedule array:
	declare -a w

	# Process the message in successive 512-bit chunks:
	for (( i=0; i<$(($final_len<<1)); i+=256 ))
	do
		chunk=${input_hex:$i:256}

		# Clear message schedule array:
		for (( j=0; j<64; j++ ))
		do
			w[$j]=00000000
		done

		# Copy chunk into schedule array:
		for (( j=0; j<16; j++ ))
		do
			w[$j]=$((0x${chunk:$(($j<<4)):16}))
		done


		# Extend the first 16 words into the remaining 64 words w[16..80] of the message schedule array:
		for (( j=16; j<80; j++ ))
		do
			sz=$(($(($(rotate ${w[$(($j-15))]} 1)^$(rotate ${w[$(($j-15))]} 8)))^$(lshft ${w[$(($j-15))]} 7)))
			so=$(($(($(rotate ${w[$(($j-2))]} 19)^$(rotate ${w[$(($j-2))]} 61)))^$(lshft ${w[$(($j-2))]} 6)))
			w[$j]=$((${w[$(($j-16))]}+$sz+${w[$(($j-7))]}+$so))
		done

		# Initialize working variables to current hash value:
		a=${h[0]}
		b=${h[1]}
		c=${h[2]}
		d=${h[3]}
		e=${h[4]}
		f=${h[5]}
		g=${h[6]}
		hay=${h[7]}

		# Compression function main loop:
		for (( j=0; j<80; j++ ))
		do
			SO=$(($(rotate $e 14)^$(rotate $e 18)^$(rotate $e 41)))
			ch=$(($(($e&$f))^$(($((~$e))&$g))))
			temp1=$(($hay+$SO+$ch+${k[$j]}+${w[$j]}))
			SZ=$(($(rotate $a 28)^$(rotate $a 34)^$(rotate $a 39)))
			maj=$(($(($a&$b))^$(($a&$c))^$(($b&$c))))
			temp2=$(($SZ+$maj))

			hay=$g
			g=$f
			f=$e
			e=$(($d+$temp1))
			d=$c
			c=$b
			b=$a
			a=$(($temp1+$temp2))
		done

		# Add the compressed chunk to the current hash value:
		h[0]=$((${h[0]}+$a))
		h[1]=$((${h[1]}+$b))
		h[2]=$((${h[2]}+$c))
		h[3]=$((${h[3]}+$d))
		h[4]=$((${h[4]}+$e))
		h[5]=$((${h[5]}+$f))
		h[6]=$((${h[6]}+$g))
		h[7]=$((${h[7]}+$hay))
	done

	# Produce the final hash value (big-endian):
	for (( i=0; i<8; i++ ))
	do
		printf "%016x" ${h[$i]}
	done
}

sha512 $1
echo # newline
