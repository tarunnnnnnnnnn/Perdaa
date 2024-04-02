import serial
from datetime import datetime

def verify_data(data):
    byte1 = data[0]
    byte2 = data[1]
    byte3 = data[2]
    byte6 = data[5]
    date_time_bytes = data[7:13]
    form_date_time = ":".join([str(byte) for byte in date_time_bytes])
    byte2524 = data[2523]
    byte2525 = data[2524]
    perd_data = data[2526:2553]

    # Verifying byte1 and byte2
    if byte1 == 0x0A and byte2 == 0xA0:
        print("OA and AO verified")

    # Verifying byte3 for device ID
    if 0x01 <= byte3 <= 0xFF:
        print("Device ID:", byte3)

    # Verifying byte6 for 09-DD
    if 0x09 <= byte6 <= 0xDD:
        print("Byte 6 verified")

    # Verifying date and time
    current_datetime = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    print("Date and Time:", current_datetime)

    # Verifying byte2524 and byte2525
    if byte2524 == 0x05 and byte2525 == 0x50:
        print("05 and 50 verified")

    # Converting PERDAA from ASCII to hexadecimal
    perd_data_hex = "".join([hex(ord(char))[2:].zfill(2) for char in perd_data])
    print("PERDAA (Hex):", perd_data_hex)

    # Printing and logging data from 16th byte to 2521st byte
    relevant_data = data[15:2521]
    relevant_data_str = "".join([chr(byte) for byte in relevant_data])
    print("Relevant Data:", relevant_data_str)

    with open("data_out.txt", "a") as file:
        file.write(f"Device ID: {byte3}, Date and Time: {current_datetime}, PERDAA (Hex): {perd_data_hex}\n")
        file.write("Relevant Data: {}\n".format(relevant_data_str))

# Initialize serial port
ser = serial.Serial('COM4', 115200, timeout=None)

while True:
    # Read data from serial port
    data = ser.read(2565)

    if len(data) == 2565:
        verify_data(data)
    else:
        print("Invalid data length:", len(data))

# Close the serial port
ser.close()
