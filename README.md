🏗️ Fase 1: Cimentación (Setup inicial)Creación de la carpeta:Abre tu terminal y ejecuta:Bashmkdir crudclinica
cd crudclinica
flutter create .
Configuración de Firebase (Consola):Ve a Firebase Console.Crea un proyecto llamado crud-clinica-antigravity.Activa Cloud Firestore en modo de prueba (test mode).Registra tu app (Android/iOS) y descarga los archivos de configuración (google-services.json).Pro Tip: Usa flutterfire configure para automatizar todo esto.Librerías (El pubspec.yaml de poder):Agrega estas líneas bajo dependencies:YAMLdependencies:
  flutter:
    sdk: flutter
  firebase_core: ^2.24.2
  cloud_firestore: ^4.14.0
Luego ejecuta: flutter pub get.🌌 Fase 2: Metodología Antigravity (Agentes y Roles)En el framework Antigravity, no vemos el código como archivos aislados, sino como Agentes con responsabilidades claras.Estructura de AgentesAgenteRolSkill (Habilidad)Data WardenArquitecto de PersistenciaGestión total de Firebase Firestore (CRUD).Interface CraftsmanDiseñador de ExperienciaRenderizado de UI y manejo de formularios.The MessengerGestor de EstadoComunicación fluida entre la base de datos y la vista.Flujo de TrabajoDefinición del Esquema: El Data Warden define los campos (Nombre, Edad, Salario).Implementación de Skills: Se crean las funciones asíncronas para disparar a Firestore.Despliegue de Interfaz: El Interface Craftsman conecta los inputs con los Skills.📁 Fase 3: Estructura de Carpetas "Pro"Así debe verse tu proyecto para que sea escalable:Plaintextcrudclinica/
├── lib/
│   ├── agents/             # Lógica de Antigravity
│   │   ├── data_warden.dart
│   ├── models/             # Estructuras de datos
│   │   └── employee_model.dart
│   ├── ui/                 # Pantallas y widgets
│   │   └── home_screen.dart
│   └── main.dart           # Punto de entrada
├── pubspec.yaml
└── ...
💻 Fase 4: El Código (Totalmente Funcional)1. El Modelo (The Skeleton)lib/models/employee_model.dartDartclass Employee {
  String id;
  String nombre;
  int edad;
  double salario;

  Employee({required this.id, required this.nombre, required this.edad, required this.salario});

  // Convertir a Map para Firebase
  Map<String, dynamic> toMap() => {
    "nombre": nombre,
    "edad": edad,
    "salario": salario,
  };
}
2. Agente: Data Warden (CRUD Skill)lib/agents/data_warden.dartDartimport 'package:cloud_firestore/cloud_firestore.dart';

class DataWarden {
  final CollectionReference _db = FirebaseFirestore.instance.collection('empleados');

  // Create
  Future<void> addEmployee(String n, int e, double s) {
    return _db.add({'nombre': n, 'edad': e, 'salario': s});
  }

  // Read (Stream)
  Stream<QuerySnapshot> getEmployees() => _db.snapshots();

  // Update
  Future<void> updateEmployee(String id, String n, int e, double s) {
    return _db.doc(id).update({'nombre': n, 'edad': e, 'salario': s});
  }

  // Delete
  Future<void> deleteEmployee(String id) => _db.doc(id).delete();
}
3. La UI: Interface Craftsmanlib/ui/home_screen.dart (Resumen funcional)Dartimport 'package:flutter/material.dart';
import '../agents/data_warden.dart';
import 'package:cloud_firestore/cloud_firestore.dart';

class HomeScreen extends StatelessWidget {
  final DataWarden warden = DataWarden();
  final TextEditingController nameCtrl = TextEditingController();
  final TextEditingController ageCtrl = TextEditingController();
  final TextEditingController salaryCtrl = TextEditingController();

  void _showForm(BuildContext context, {String? id}) {
    showModalBottomSheet(
      context: context,
      isScrollControlled: true,
      builder: (_) => Padding(
        padding: EdgeInsets.only(bottom: MediaQuery.of(context).viewInsets.bottom, left: 20, right: 20, top: 20),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            TextField(controller: nameCtrl, decoration: InputDecoration(labelText: 'Nombre')),
            TextField(controller: ageCtrl, decoration: InputDecoration(labelText: 'Edad'), keyboardType: TextInputType.number),
            TextField(controller: salaryCtrl, decoration: InputDecoration(labelText: 'Salario'), keyboardType: TextInputType.number),
            ElevatedButton(
              onPressed: () {
                if (id == null) {
                  warden.addEmployee(nameCtrl.text, int.parse(ageCtrl.text), double.parse(salaryCtrl.text));
                } else {
                  warden.updateEmployee(id, nameCtrl.text, int.parse(ageCtrl.text), double.parse(salaryCtrl.text));
                }
                Navigator.pop(context);
              },
              child: Text(id == null ? 'Crear' : 'Actualizar'),
            )
          ],
        ),
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('CRUD Clinica - Antigravity')),
      body: StreamBuilder(
        stream: warden.getEmployees(),
        builder: (context, AsyncSnapshot<QuerySnapshot> snapshot) {
          if (!snapshot.hasData) return Center(child: CircularProgressIndicator());
          return ListView(
            children: snapshot.data!.docs.map((doc) {
              return ListTile(
                title: Text(doc['nombre']),
                subtitle: Text('Edad: ${doc['edad']} - \$${doc['salario']}'),
                trailing: Row(
                  mainAxisSize: MainAxisSize.min,
                  children: [
                    IconButton(icon: Icon(Icons.edit), onPressed: () => _showForm(context, id: doc.id)),
                    IconButton(icon: Icon(Icons.delete), onPressed: () => warden.deleteEmployee(doc.id)),
                  ],
                ),
              );
            }).toList(),
          );
        },
      ),
      floatingActionButton: FloatingActionButton(onPressed: () => _showForm(context), child: Icon(Icons.add)),
    );
  }
}
4. Punto de Entradalib/main.dartDartimport 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'ui/home_screen.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(); // Inicialización de fuego
  runApp(MaterialApp(
    theme: ThemeData(primarySwatch: Colors.red), // Al carbón
    home: HomeScreen(),
  ));
}
🚀 Conclusión del Creador de SoftwareEste flujo Antigravity separa la lógica del "Data Warden" de la vista, permitiendo que los estudiantes entiendan que el código no es un espagueti, sino un equipo de agentes trabajando juntos.
