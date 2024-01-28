-# Import necessary libraries
from PIL import Image

# Function to encode data into an image using LSB (Least Significant Bit) steganography
def encode_data(image_path, data):
    img = Image.open(image_path)

    # Convert data to binary
    binary_data = ''.join(format(ord(char), '08b') for char in data)

    # Ensure the image has enough capacity for the data
    if len(binary_data) > img.width * img.height:
        raise Exception("Data size exceeds image capacity")

    data_index = 0

    # Iterate through each pixel and modify the LSB to encode data
    for i in range(img.width):
        for j in range(img.height):
            pixel = list(img.getpixel((i, j)))

            # Encode data in the least significant bit of each color channel
            for color_channel in range(3):  # RGB channels
                if data_index < len(binary_data):
                    pixel[color_channel] = pixel[color_channel] & ~1 | int(binary_data[data_index])
                    data_index += 1

            img.putpixel((i, j), tuple(pixel))

    # Save the encoded image
    img.save('encoded_image.png')

# Function to decode data from an image using LSB steganography
def decode_data(encoded_image_path):
    img = Image.open(encoded_image_path)

    binary_data = ""

    # Iterate through each pixel and extract LSB from each color channel
    for i in range(img.width):
        for j in range(img.height):
            pixel = list(img.getpixel((i, j)))

            # Extract LSB from each color channel
            for color_channel in range(3):  # RGB channels
                binary_data += str(pixel[color_channel] & 1)

    # Convert binary data to ASCII
    decoded_data = ''.join([chr(int(binary_data[i:i+8], 2)) for i in range(0, len(binary_data), 8)])

    return decoded_data

# Example usage
data_to_encode = "Hello, this is a sample message for steganography"
image_path = "original_image.png"

# Encode data into the image
encode_data(image_path, data_to_encode)

# Decode data from the encoded image
decoded_data = decode_data("encoded_image.png")

# Display the results
print(f"Original Data: {data_to_encode}")
print(f"Decoded Data: {decoded_data}")
 
