## How JPEG works

JPEG makes use of how human eyes work:

- Rods: brightness & low light senstive (100 milion cells)
- Cones: Color sensitive (6 milion cells)

There are 5 key steps:

1. Color space conversion: convert rgb to red/blue chrominance and luminance.
2. Chrominance downsampling: Since human eyes is better at luminance than chrominance, we reduce the quality of chrominance and then merge back with lumninance.
   ![chrominance](https://i.imgur.com/KllSwIy.png)
3. Discrete cosine transform
4. Quantization: Take the fact that our eyes are not good at high frequency and out of focus elements -> remove them.
   ![frequency](https://i.imgur.com/UY4fUTD.jpg)
5. Run length and Huffman encoding

JPEG is great for camera photos because natual world is tends to have smooth textures, it doesn't work well for vector graphics.

### Resources

- [How are Images Compressed?](https://www.youtube.com/watch?v=Kv1Hiv3ox8I)
