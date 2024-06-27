# Multithreaded-Chat-server
#A multithreaded client/server ChatServer based on console input/output, which uses Java Socket programming. The server listens for connection requests from clients across the network or even from the same machine. Clients connect to the server using an IP address and port number. After connecting to the server, each client chooses a username for the chat room. When a client sends a message, it is transmitted to the server using ObjectOutputStream in Java. Upon receiving a message from a client, the server broadcasts it to all clients if it is not a private message. If the message is private, which is indicated by '@' followed by a valid username, the server sends the message only to the specified user. The system uses Java object serialization to transfer messages between clients and the server.
//ChatClient.java

package project;
import java.io.*;
import java.net.*;
import java.util.Scanner;

public class ChatClient {
    private static final String SERVER_ADDRESS = "localhost";
    private static final int SERVER_PORT = 12345;

    public static void main(String[] args) {
        try (Socket socket = new Socket(SERVER_ADDRESS, SERVER_PORT);
             PrintWriter out = new PrintWriter(socket.getOutputStream(), true);
             BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()))) {

            Scanner scanner = new Scanner(System.in);

            // Send client ID to the server
            System.out.print("Enter your client ID: ");
            String clientId = scanner.nextLine();
            out.println(clientId);

            // Start a new thread to listen for messages from the server
            Thread listenerThread = new Thread(() -> {
                String serverMessage;
                try {
                    while ((serverMessage = in.readLine()) != null) {
                        System.out.println(serverMessage);
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
            });
            listenerThread.start();

            // Read messages from the console and send them to the server
            String message;
            while (scanner.hasNextLine()) {
                message = scanner.nextLine();
                out.println(message);
            }

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

