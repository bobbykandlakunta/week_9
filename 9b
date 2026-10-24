import 'dart:convert';

import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;

// ---------------------- DATA MODEL ----------------------

class User {
  final int id;
  final String name;
  final String email;
  final String phone;

  const User({
    required this.id,
    required this.name,
    required this.email,
    required this.phone,
  });

  factory User.fromJson(Map<String, dynamic> json) {
    return User(
      id: json['id'] is int
          ? json['id'] as int
          : int.tryParse(json['id'].toString()) ?? 0,
      name: json['name']?.toString() ?? 'N/A',
      email: json['email']?.toString() ?? 'N/A',
      phone: json['phone']?.toString() ?? 'N/A',
    );
  }
}

// ---------------------- FETCH FUNCTION ----------------------

Future<List<User>> fetchUsers() async {
  const String url =
      'https://jsonplaceholder.typicode.com/users';

  try {
    final response = await http.get(
      Uri.parse(url),
    );

    if (response.statusCode == 200) {
      final dynamic decodedData =
          jsonDecode(response.body);

      if (decodedData is List) {
        return decodedData
            .map(
              (item) => User.fromJson(
                item as Map<String, dynamic>,
              ),
            )
            .toList();
      }

      throw Exception('Invalid response format');
    } else {
      throw Exception(
        'Server returned status code: ${response.statusCode}',
      );
    }
  } catch (e) {
    throw Exception(
      'Failed to load users: $e',
    );
  }
}

// ---------------------- MAIN APP ----------------------

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      debugShowCheckedModeBanner: false,
      home: UserListScreen(),
    );
  }
}

// ---------------------- USER LIST SCREEN ----------------------

class UserListScreen extends StatefulWidget {
  const UserListScreen({super.key});

  @override
  State<UserListScreen> createState() =>
      _UserListScreenState();
}

class _UserListScreenState
    extends State<UserListScreen> {
  late Future<List<User>> _futureUsers;

  @override
  void initState() {
    super.initState();

    _futureUsers = fetchUsers();
  }

  void _retry() {
    setState(() {
      _futureUsers = fetchUsers();
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('User List'),
        backgroundColor: Colors.teal,
        foregroundColor: Colors.white,
      ),

      body: FutureBuilder<List<User>>(
        future: _futureUsers,

        builder: (context, snapshot) {
          if (snapshot.connectionState ==
              ConnectionState.waiting) {
            return const Center(
              child: CircularProgressIndicator(),
            );
          }

          if (snapshot.hasError) {
            return Center(
              child: Padding(
                padding: const EdgeInsets.all(20),
                child: Column(
                  mainAxisAlignment:
                      MainAxisAlignment.center,
                  children: [
                    const Icon(
                      Icons.error_outline,
                      size: 50,
                      color: Colors.red,
                    ),

                    const SizedBox(height: 10),

                    Text(
                      '${snapshot.error}',
                      textAlign: TextAlign.center,
                    ),

                    const SizedBox(height: 20),

                    ElevatedButton(
                      onPressed: _retry,
                      child: const Text('Retry'),
                    ),
                  ],
                ),
              ),
            );
          }

          if (!snapshot.hasData ||
              snapshot.data!.isEmpty) {
            return const Center(
              child: Text(
                'No users found',
              ),
            );
          }

          final List<User> users =
              snapshot.data!;

          return ListView.builder(
            itemCount: users.length,

            itemBuilder: (context, index) {
              final User user = users[index];

              return Card(
                margin:
                    const EdgeInsets.symmetric(
                  horizontal: 16,
                  vertical: 8,
                ),
                elevation: 3,

                child: ListTile(
                  leading: CircleAvatar(
                    backgroundColor:
                        Colors.teal.shade100,
                    child: Text(
                      user.id.toString(),
                    ),
                  ),

                  title: Text(
                    user.name,
                    style: const TextStyle(
                      fontWeight: FontWeight.bold,
                    ),
                  ),

                  subtitle: Column(
                    crossAxisAlignment:
                        CrossAxisAlignment.start,
                    children: [
                      Text(user.email),
                      Text(user.phone),
                    ],
                  ),

                  trailing: IconButton(
                    icon: const Icon(
                      Icons.phone,
                    ),
                    onPressed: () {
                      ScaffoldMessenger.of(
                        context,
                      ).showSnackBar(
                        SnackBar(
                          content: Text(
                            'Call ${user.phone}',
                          ),
                        ),
                      );
                    },
                  ),
                ),
              );
            },
          );
        },
      ),
    );
  }
}





