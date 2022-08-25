# Ścieżka w folderze z d
```d
dmd2\windows\bin
```
# Terminal Output
```d
	import std.stdio;
	void main(char[][] args){
		int a = 5;
		writeln(a);
	}
```



# Pliki i tablice asocjacyjne
```d
import std.stdio;
import std.uni;
import std.string;

void main(char[][] args){
    int[string] words;
    File* plik = new File(args[1], "r");
    string linia;
    while((linia = plik.readln) !is null){
        foreach(line; linia.split(" ")){
            line = chomp(line);
            line = chomp(line, ",");
            line = chomp(line, "!");
            line = chomp(line, ";");
            ++words[line.toLower()];
        }
    }
    foreach(key; words.keys){
        writeln(key, ": ", words[key]);
    }
}
```

# Argumenty
```d
	import std.stdio;
    import std.conv;
	void main(char[][] args){
		int a = to!int(args[1]);
		string b = args[2].dup;
		writeln(a);
        writeln(b);
	}
```



# Wątki
```d
import std.stdio;
import std.conv;
import core.thread;

class MyThread : Thread{
    int[] tab;
    int wynik = 0;

    this(int[] tab){
        this.tab = tab;
        super(&akcja);
    }

    void akcja(){
        for(int i = 0; i < tab.length; ++i){
            wynik += tab[i];
        }
    }

}



void main(char[][] args){
    int[][] tab;
    int m = to!int(args[1]);
    int n = to!int(args[2]);
    tab.length = m;
    for(int i = 0; i < m; ++i){
        tab[i].length = n;
        for(int j = 0; j < n; ++j){
            tab[i][j] = i * n + j;
            write(tab[i][j], " ");
        }
        writeln();
    }

    ThreadGroup grupa = new ThreadGroup();

    for(int i = 0; i < m; ++i){
        grupa.add(new MyThread(tab[i]).start());
    }

    grupa.joinAll();
    MyThread watek;
    int wynik = 0;
    foreach(Thread x ; grupa){
        watek = cast(MyThread) x;
        wynik+= watek.wynik;
    }

    write("WyniK: ", wynik);
}  
```


# UDP Client
```d
import std.stdio;
import std.socket;
import std.string;

void main(char[][] args){
    Socket socket = new Socket(AddressFamily.INET, SocketType.DGRAM, ProtocolType.UDP);
    string tekst;
    ubyte[] bufor;
    while(true){
        bufor = new ubyte[1000];
        tekst = readln();
        tekst = chomp(tekst);
        if(tekst == "") break;
        socket.sendTo(tekst, getAddress("127.0.0.1", 2137)[0]);
        socket.receive(bufor);
        tekst = cast(string)bufor;
        writeln(tekst);
    }
}   
		}
```
# UDP Server
``` d
import std.stdio;
import std.socket;
import std.string;

void main(char[][] args){
    UdpSocket serwer = new UdpSocket();
    serwer.bind(new InternetAddress(2137));
    serwer.blocking = true;
    ubyte[] message;
    Address adres;
    string wiadomosc;
    while(true){
        message = new ubyte[1000];
        serwer.receiveFrom(message, adres);
        wiadomosc = cast(string) message;
        serwer.sendTo(wiadomosc.toUpper, adres);
    }
}
```

# TCP Client
``` d
import std.stdio;
import std.socket;
import std.string;
import std.conv;
import std.uni;

string ubyteToString(ubyte[] buff)
{
	string str = "";
	for (int i = 0; i < buff.length && buff[i] != 0; i++)
	{
		str ~= to!char(buff[i]);
	}
	return str;
}


void main(char[][] args){
    Socket socket = new Socket(AddressFamily.INET, SocketType.STREAM, ProtocolType.TCP);
    socket.connect(getAddress("127.0.0.1", 2137)[0]);
    string tekst;
    write("Podaj wiadomosc: ");
    while(true){
        tekst = readln();
        tekst = chomp(tekst);
        ubyte[1000] message;
        socket.send(tekst);
        if(tekst == "") break;
        socket.receive(message);
        writeln(cast(string) message);
        write("Podaj wiadomosc: ");
    }
}
```
# TCP Server
```d
import std.socket;
import core.thread;
import std.stdio;
import std.string;

class Connection : Thread{
    Socket socket;

    this(Socket socket){
        this.socket = socket;
        super(&akcja);
    }


    void akcja(){
        auto buf = new ubyte[1000];
        string wynik;
        while(true){
            buf = new ubyte[1000];
            socket.receive(buf);
            wynik = cast(string) buf;
            if(buf[0] == 0) break;
            socket.send(wynik.toUpper());
        }
        writeln("Zamykam socket");
        socket.close();
    }
}



void main(){
    TcpSocket socket = new TcpSocket();
    socket.bind(new InternetAddress(2137));
    socket.listen = 1;
    socket.blocking = true;
    Socket client;
    writeln("Serwer dziala");
    while(true){
        client = socket.accept();
        Connection connect = new Connection(client);
        connect.isDaemon(true);
        connect.start();
    }
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
import core.thread;
import std.conv;
import std.random;
import std.stdio;
import core.sync.mutex;

class MyThread: Thread{
    #tylko funkcja uzywajaca mutex (konstruktor trzeba zrobic)
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

# Przeciazenia
```d
import std.stdio;

class MyClass{
    int[10] tab;
    int zmienna = 10;

    this(){
        for(int i = 0; i < 10; ++i){
            tab[i] = i;
        }
    }

    int opCall(int a, int b){
        writeln("Obicieta", tab[0..5]);
        return a * b;
    }
    int opUnary(string operator)(){
        if(operator == "-") return zmienna * 2;
        if(operator == "+") return zmienna / 2;
        else assert(0);
    }
    int opIndexUnary(string operator)(int index){
        if(operator == "-") return tab[index] * 2;
        if(operator == "+") return tab[index] / 2;
        else assert(0);
    }
    int opBinary(string operator)(int liczba){
        if(operator == "-") return zmienna + liczba;
        if(operator == "+") return zmienna - liczba;
        else assert(0, "nie obslugujemy takiego");
    }

    int opIndex(int index){
        return tab[index];
    }

    void opIndexAssign(int liczba, int index){
        tab[index] = liczba;
    }

    bool opEquals(int value) const{
        if(value == zmienna) return false;
        else return true;
    }

}

void main(){
    MyClass klasa = new MyClass();
    writeln(klasa(10,20));
    writeln(-klasa);
    writeln(+klasa);
    writeln(-klasa[4]);
    writeln(+klasa[4]);
    writeln(klasa + 1);
    writeln(klasa - 1);
    writeln(klasa[2]);
    klasa[2] = 5;
    writeln(klasa[2]);
    writeln(klasa == 10);
    writeln(klasa == 5);
}
```


# Klient TCP z intem
```d
import std.stdio;
import std.socket;
import std.string;
import std.conv;


void main(char[][] args){
    Socket socket = new Socket(AddressFamily.INET, SocketType.STREAM, ProtocolType.TCP);

    socket.connect(getAddress("127.0.0.1", 2137)[0]);
    int liczba;
    char[] buf;
    string ciag;
    while(true){
        readln(buf);
        if(buf == "0\n") break;
        socket.send(buf);
        socket.receive(buf);
        ciag = buf.dup;
        ciag = chomp(ciag);
        liczba = parse!int(ciag);
        writeln("Dostalem odpowiedz: ", liczba);
    }
}
```


# Server TCP z intem
```d
import std.socket;
import std.stdio;
import std.conv;

void main(){
    TcpSocket socket = new TcpSocket();
    socket.bind(new InternetAddress(2137));
    socket.listen = 1;
    socket.blocking = true;
    Socket client;
    client = socket.accept();
    writeln("poczatek polaczenia");
    ubyte[] buf;
    int wynik;
    string ciag;
    while(true){
        buf = new ubyte[1000];
        client.receive(buf);
        if(buf[0] == 0) break;
        ciag = cast(string) buf;
        wynik = parse!int(ciag);
        if(wynik == 0) break;
        wynik *= 2;
        ciag = to!string(wynik);
        client.send(ciag);
    }
    writeln("koniec polaczenia");
    socket.close();
}   
```