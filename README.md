flutter create ping_app
cd ping_app
import 'package:flutter/material.dart';
import 'package:ping_app/screens/home_screen.dart';

void main() {
  runApp(const PingApp());
}

class PingApp extends StatelessWidget {
  const PingApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'PING',
      theme: ThemeData(
        brightness: Brightness.light,
        useMaterial3: true,
      ),
      darkTheme: ThemeData(
        brightness: Brightness.dark,
        useMaterial3: true,
      ),
      themeMode: ThemeMode.system,
      home: const HomeScreen(),
      debugShowCheckedModeBanner: false,
    );
  }
}
import 'package:flutter/material.dart';
import 'package:ping_app/screens/nearby_screen.dart';

class HomeScreen extends StatelessWidget {
  const HomeScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: ElevatedButton(
          child: const Text('Go Visible'),
          onPressed: () {
            Navigator.push(
              context,
              MaterialPageRoute(builder: (_) => const NearbyScreen()),
            );
          },
        ),
      ),
    );
  }
}
import 'package:flutter/material.dart';
import 'package:ping_app/models/fake_user.dart';
import 'package:ping_app/screens/profile_screen.dart';

class NearbyScreen extends StatelessWidget {
  const NearbyScreen({super.key});

  static final List<FakeUser> fakeUsers = [
    FakeUser(name: 'Jasmine', imageUrl: 'assets/images/user1.jpg', distanceMeters: 23),
    FakeUser(name: 'Noah', imageUrl: 'assets/images/user2.jpg', distanceMeters: 18),
    FakeUser(name: 'Leila', imageUrl: 'assets/images/user3.jpg', distanceMeters: 55),
    // Add more fake users
  ];

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Nearby Users')),
      body: ListView.builder(
        itemCount: fakeUsers.length,
        itemBuilder: (context, index) {
          final user = fakeUsers[index];
          return ListTile(
            leading: CircleAvatar(backgroundImage: AssetImage(user.imageUrl)),
            title: Text(user.name),
            subtitle: Text('${user.distanceMeters} meters away'),
            onTap: () {
              Navigator.push(
                context,
                MaterialPageRoute(
                  builder: (_) => ProfileScreen(user: user),
                ),
              );
            },
          );
        },
      ),
    );
  }
}
import 'package:flutter/material.dart';
import 'package:ping_app/models/fake_user.dart';
import 'package:ping_app/screens/chat_screen.dart';
import 'dart:math';

class ProfileScreen extends StatelessWidget {
  final FakeUser user;

  const ProfileScreen({super.key, required this.user});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text(user.name)),
      body: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          CircleAvatar(radius: 80, backgroundImage: AssetImage(user.imageUrl)),
          const SizedBox(height: 20),
          Text('${user.name}, ${user.distanceMeters} meters away'),
          const SizedBox(height: 20),
          ElevatedButton(
            child: const Text('Send PING'),
            onPressed: () {
              final accepted = Random().nextBool();
              if (accepted) {
                Navigator.pushReplacement(
                  context,
                  MaterialPageRoute(builder: (_) => ChatScreen(user: user)),
                );
              } else {
                ScaffoldMessenger.of(context).showSnackBar(
                  const SnackBar(content: Text('PING Declined')),
                );
              }
            },
          ),
        ],
      ),
    );
  }
}
import 'package:flutter/material.dart';
import 'package:ping_app/models/fake_user.dart';
import 'dart:async';
import 'dart:math';

class ChatScreen extends StatefulWidget {
  final FakeUser user;

  const ChatScreen({super.key, required this.user});

  @override
  State<ChatScreen> createState() => _ChatScreenState();
}

class _ChatScreenState extends State<ChatScreen> {
  final List<String> messages = [];
  final TextEditingController controller = TextEditingController();
  final Random random = Random();

  void sendMessage(String text) {
    setState(() {
      messages.add('me:$text');
    });
    controller.clear();

    Timer(Duration(seconds: random.nextInt(2) + 1), () {
      setState(() {
        messages.add('bot:${generateBotReply()}');
      });
    });
  }

  String generateBotReply() {
    final replies = [
      "Haha true!",
      "That's interesting!",
      "Tell me more!",
      "Lol, you're funny!",
      "Cool!",
    ];
    return replies[random.nextInt(replies.length)];
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text(widget.user.name)),
      body: Column(
        children: [
          Expanded(
            child: ListView.builder(
              itemCount: messages.length,
              itemBuilder: (context, index) {
                final parts = messages[index].split(':');
                final isMe = parts[0] == 'me';
                return Align(
                  alignment: isMe ? Alignment.centerRight : Alignment.centerLeft,
                  child: Container(
                    margin: const EdgeInsets.all(8),
                    padding: const EdgeInsets.all(12),
                    decoration: BoxDecoration(
                      color: isMe ? Colors.blueAccent : Colors.grey.shade300,
                      borderRadius: BorderRadius.circular(16),
                    ),
                    child: Text(parts[1], style: TextStyle(color: isMe ? Colors.white : Colors.black)),
                  ),
                );
              },
            ),
          ),
          Padding(
            padding: const EdgeInsets.all(8.0),
            child: Row(
              children: [
                Expanded(
                  child: TextField(controller: controller),
                ),
                IconButton(
                  icon: const Icon(Icons.send),
                  onPressed: () {
                    if (controller.text.isNotEmpty) sendMessage(controller.text);
                  },
                )
              ],
            ),
          ),
        ],
      ),
    );
  }
}
class FakeUser {
  final String name;
  final String imageUrl;
  final int distanceMeters;

  FakeUser({required this.name, required this.imageUrl, required this.distanceMeters});
}
name: ping_app
description: PING Prototype App
version: 1.0.0+1
environment:
  sdk: ">=3.2.0 <4.0.0"

dependencies:
  flutter:
    sdk: flutter
  provider: ^6.0.5
  lottie: ^2.6.0
  audioplayers: ^5.2.1

dev_dependencies:
  flutter_test:
    sdk: flutter

flutter:
  uses-material-design: true
  assets:
    - assets/images/
    - assets/sounds/
