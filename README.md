invented the bicycle)) Simple fork of goburrow/serial to use setterm() from termios lib AND use non-standart (non-posix) termios.Cflag 0x40000000, to manipulate 9 bit's MARK and SPACE states. 
Changes (after original goburrow/serial):
1) serial_posix.go: newTermios(&config) => NewTermios(&config) (now it is public)
2) serial.go: add SetTermios(termios) to Port interface (not need to reopen port to apply new termios settings)
3) serial_posix.go: add 0x40000000 bit ops, non-posix. tested  linux only (new debian 9.8 and ti arm embed with old kernel 2.6.x)
case "", "E":	// As mentioned in the modbus spec, the default parity mode must be Even parity
		// PARENB: Enable parity generation on output.
		termios.Cflag |= syscall.PARENB
		// INPCK: Enable input parity checking.
		termios.Iflag |= syscall.INPCK
		termios.Cflag &^= 0x40000000
case "M":
		termios.Cflag |= 0x40000000
		termios.Cflag |= syscall.PARODD
case "S":
		termios.Cflag |= 0x40000000
		termios.Cflag |= syscall.PARENB


Added port.Setterm  

## Example
```go
package main

import (
	"log"

	"github.com/adenis78/serial"
)

func main() {
	port, err := serial.Open(&serial.Config{Address: "/dev/ttyUSB0"})
	if err != nil {
		log.Fatal(err)
	}
	defer port.Close()

	_, err = port.Write([]byte("serial"))
	if err != nil {
		log.Fatal(err)
	}
	
	termios, _ := serial.NewTermios(&config)
	err = port.SetTermios(termios)
	if err != nil {
		log.Fatal(err)
	}
}
```
## Testing

### Linux and Mac OS
- `socat -d -d pty,raw,echo=0 pty,raw,echo=0`
- on Mac OS, the socat command can be installed using homebrew:
	````brew install socat````

### Windows
- [Null-modem emulator](http://com0com.sourceforge.net/)
- [Terminal](https://sites.google.com/site/terminalbpp/)
