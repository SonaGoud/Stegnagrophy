# Stegnagrophy
Encoding
import cv2
import numpy as np

# Load and convert the image to grayscale
original = cv2.imread("cat.jpg", cv2.IMREAD_GRAYSCALE)
stego = np.copy(original)

# Message to hide
message = input("Enter the message to hide: ") + chr(0)  # Null terminator to mark end
binary_message = ''.join(format(ord(char), '08b') for char in message)

# Embed message into the LSB of the image
rows, cols = stego.shape
idx = 0

for i in range(rows):
    for j in range(cols):
        if idx < len(binary_message):
            stego[i, j] = (stego[i, j] & ~1) | int(binary_message[idx])
            idx += 1

# Save the stego image
cv2.imwrite("stego_image.png", stego)

# Show original and stego images
cv2.imshow("Original", original)
cv2.imshow("Stego Image", stego)
cv2.waitKey(0)
cv2.destroyAllWindows()
Decoding
# Load the stego image
stego = cv2.imread("stego_image.png", cv2.IMREAD_GRAYSCALE)

rows, cols = stego.shape
bits = []

for i in range(rows):
    for j in range(cols):
        bits.append(str(stego[i, j] & 1))

# Convert bits to characters
chars = []
for i in range(0, len(bits), 8):
    byte = bits[i:i+8]
    char = chr(int(''.join(byte), 2))
    if char == chr(0):  # Null character as terminator
        break
    chars.append(char)

decoded_message = ''.join(chars)
print("Decoded Message:", decoded_message)
