import 'dart:convert';

import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;

void main() {
  runApp(const MyApp());
}

// ---------------- MAIN APP ----------------

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return const MaterialApp(
      debugShowCheckedModeBanner: false,
      home: UsersPage(),
    );
  }
}

// ---------------- USER MODEL ----------------

class User {
  final int id;
  final String name;
  final String email;

  const User({
    required this.id,
    required this.name,
    required this.email,
  });

  factory User.fromJson(Map<String, dynamic> json) {
    return User(
      id: json['id'] is int
          ? json['id'] as int
          : int.tryParse(json['id'].toString()) ?? 0,
      name: json['name']?.toString() ?? 'N/A',
      email: json['email']?.toString() ?? 'N/A',
    );
  }
}

// ---------------- USERS PAGE ----------------

class UsersPage extends StatefulWidget {
  const UsersPage({super.key});

  @override
  State<UsersPage> createState() => _UsersPageState();
}

class _UsersPageState extends State<UsersPage> {
  late Future<List<User>> _futureUsers;

  @override
  void initState() {
    super.initState();

    _futureUsers = fetchUsers();
  }

  // Fetch users from API
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

        throw Exception('Invalid API response');
      } else {
        throw Exception(
          'Failed to load users. '
          'Status code: ${response.statusCode}',
        );
      }
    } catch (e) {
      throw Exception(
        'Unable to fetch users: $e',
      );
    }
  }

  // Retry API request
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
          // Loading
          if (snapshot.connectionState ==
              ConnectionState.waiting) {
            return const Center(
              child: CircularProgressIndicator(),
            );
          }

          // Error
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

                    const SizedBox(height: 15),

                    Text(
                      'Error: ${snapshot.error}',
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

          // No data
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

          // Display users
          return ListView.separated(
            itemCount: users.length,

            separatorBuilder: (context, index) {
              return const Divider(
                height: 1,
              );
            },

            itemBuilder: (context, index) {
              final User user = users[index];

              return ListTile(
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

                subtitle: Text(
                  user.email,
                ),

                trailing: const Icon(
                  Icons.person,
                ),
              );
            },
          );
        },
      ),
    );
  }
}


