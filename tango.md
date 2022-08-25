
# Terminal Output
```d
	import tango.io.Stdout;
	void main(char[][] args){
		int a = 5;
		Stdout(a).newline;
	}
```



# Pliki 
```d
import tango.io.device.File;

auto file = new File(args[1]);
auto content = new char[file.length];
file.read(content);
```

```d
import tango.io.device.File;
char[] file = cast(char[])File.get(args[1]);
```

# Argumenty
```d
	import tango.io.Stdout;
	import Integer = tango.text.convert.Integer;
	void main(char[][] args){
		int a = Integer.toInt(args[1]);
		char[] b = args[2];
		Stdout(a).newline;
		Stdout(b).newline;
	}
```



# Wątki
```d
class MyThread : Thread{
    this(real[] tab){
        localTab = tab;
        super(&sum);
    }

    real getWynik(){
        return wynik;
    }

    private:
        real wynik;
        real[] localTab;

        void sum(){
            wynik = 0;
            for(int i = 0; i < localTab.length; ++i){
                wynik += localTab[i] * localTab[i];
            }
        }
}

void main(char[][] args){

	ThreadGroup grupa = new ThreadGroup;
	MyThread watek;
	for(int i = 0; i < m; ++i){
		watek = new MyThread(tab[i]);
		grupa.add(watek);
		watek.start();
	}
	grupa.joinAll;
}
```


# UDP Client
```d
		import tango.io.Stdout;
		import tango.net.device.Datagram;
		import tango.core.Thread;
		import Integer = tango.text.convert.Integer;


		void main(char[][] args){
			auto address = new IPv4Address(args[1], Integer.toInt(args[2]));
			auto gniazdo = new Datagram();
			char[512] bufor;

			gniazdo.write(args[3], address);
			gniazdo.read(bufor, address);
			Stdout(bufor).newline;
		}
```
# UDP Server
``` d
		import tango.io.Stdout;
		import tango.core.Thread;
		import tango.net.device.Datagram;
		import Integer = tango.text.convert.Integer;


		void main(char[][] args){
			Datagram socket = new Datagram();
			auto address = new IPv4Address(Integer.toInt(args[1]));
			int size;
			char[512] bufor;

			socket.bind(address);
			for(;;){
				size = socket.read(bufor, address);
				size = socket.write(bufor[0..size], address);
				Stdout(bufor).newline;
			}
		}
```

# TCP Client
``` d
		import tango.io.Stdout;
		import tango.net.device.Socket;
		import tango.core.Thread;
		import  Integer = tango.text.convert.Integer;

		void main(char[][] args){
			Socket socket = new Socket();

			char[100] bufor;

			socket.connect(args[1], Integer.toInt(args[2]));
			socket.write(args[3]);
			socket.read(bufor);
			Stdout(bufor).newline;
		}
```
# TCP Server
```d
		import tango.io.Stdout;
		import tango.net.device.Socket;
		import tango.core.Thread;
		import Integer = tango.text.convert.Integer;

		class MyThread: Thread{
			private Socket socket;


			void akcja(){
				char[512] bufor;
				int wielkosc;
				wielkosc = socket.read(bufor);
				Stdout(bufor).newline;
				wielkosc = socket.write(bufor[0..wielkosc]);
				socket.shutdown.close;
			}
			this(Socket so){
				socket = so;
				super(&akcja);
			}
		}


		void main(char[][] args){
			ServerSocket gniazdo = new ServerSocket(Integer.toInt(args[1]));
			Socket client;
			MyThread thread;

			for(;;){
				client = gniazdo.accept();
				thread = new MyThread(client);
				thread.isDaemon(true);
				thread.start();
			}
		}
```


# Tablice asocjacyjne
```d
import tango.io.device.File;
import tango.io.Stdout;

void main(char[][] args){
    int[char] letters;
    int[char[]] words;
    auto file = new File(args[1]);
    int lastWhite;
    auto content = new char[file.length];
    file.read(content);
    for(int i = 0; i < file.length; ++i){
        if(content[i] == '\n') Stdout("Koniec linii").newline;
        if(content[i] == ' ' || content[i] == '\n'){
                ++words[content[lastWhite..i]];
                lastWhite = i + 1;
        }
        ++letters[content[i]];
    }
    ++words[content[lastWhite..file.length]];
    Stdout(letters).newline;
    foreach(key; words.keys){
        Stdout(key)("\t");
        Stdout(words[key]).newline;
    }
}
```

# Szyfrowanie pliku
```d
import tango.util.digest.Md5;
import tango.util.digest.Sha256;
import tango.io.device.File;
import tango.io.Stdout;

void main(char[][] args) {
    char[] file = cast(char[])File.get(args[1]);
    Md5 md5 = new Md5();
    Sha256 sha = new Sha256();
    md5.update(cast(ubyte[]) file);
    Stdout(md5.hexDigest).newline;
    sha.update(cast(ubyte[]) file);
    Stdout(sha.hexDigest).newline;
}
```
# Instrukcja wykonywana tylko przez 1 watek
```d
void add(){
            sum = 0;
            for(int i = 0; i < tab.length; ++i){
                sum += tab[i] * tab[i];
            }
            synchronized{
                *scharedSuma += sum;
            }
        }
```

# Mutex
```d
import tango.core.Thread;
import Integer = tango.text.convert.Integer;
import tango.math.random.Random;
import tango.io.Stdout;
import tango.core.sync.Mutex;

class MyThread: Thread{
    
        void add(){
            sum = 0;
            for(int i = 0; i < tab.length; ++i){
                sum += tab[i] * tab[i];
            }
            mutex.lock();
            *scharedSuma += sum;
            mutex.unlock();
        }
    
}

void main(char[][] args){
    Mutex mutex = new Mutex;
    MyThread thread;

    ThreadGroup grupa = new ThreadGroup;
    for(int i = 0; i < n; ++i){
        thread = new MyThread(tab[i], &suma, mutex);
        grupa.add(thread);
        thread.start();
    }
    grupa.joinAll;
}

```

# OPcall
```d
import tango.io.Stdout;
import tango.math.Math;
class Sin {
    double n;
    double m;
    this(double n, double m) {
        this.n = n;
        this.m = m;
    }
    double opCall(double x) {
        return n * sin(m*x);
    }
}
void main() {
    auto mySin = new Sin(2, 0.75);
    Stdout(mySin(3.1415)).newline;
}
```
