¡A la burger! 🍔🔥 Aquí tienes el plan de trabajo definitivo, diseñado "al carbón" para que tu proyecto sea una joya de la ingeniería de software. Vamos a usar la metodología Antigravity para que no solo sea un CRUD, sino un sistema orquestado por agentes.🏗️ 1. Cimentación: Estructura de CarpetasAbre tu terminal y ejecuta estos comandos para crear el entorno:Bashmkdir xfluttercarrasco0421
cd xfluttercarrasco0421
flutter create crudALABURGER
cd crudALABURGER
Estructura de Carpetas (Arquitectura Antigravity)PlaintextcrudALABURGER/
├── lib/
│   ├── agents/            # Lógica de agentes (Roles y Skills)
│   │   ├── data_warden.dart
│   │   └── interface_craftsman.dart
│   ├── models/            # Definición de la entidad
│   │   └── product_model.dart
│   ├── ui/                # Interfaz de usuario
│   │   └── inventory_screen.dart
│   └── main.dart          # Punto de entrada
└── pubspec.yaml           # Dependencias
🔥 2. Configuración de Firebase y DependenciasPaso 3: Consola FirebaseVe a Firebase Console.Crea un proyecto llamado alaburger-crud.Activa Cloud Firestore y crea la colección productos.Define las reglas en "modo de prueba".Paso 4 y 5: Integración de LibreríasModifica tu archivo pubspec.yaml agregando estas líneas bajo dependencies:YAMLdependencies:
  flutter:
    sdk: flutter
  firebase_core: ^2.24.2
  cloud_firestore: ^4.14.0
Ejecuta en terminal: flutter pub get🤖 3. Metodología Antigravity: Agentes y RolesPara esta práctica guiada, definiremos el flujo de trabajo mediante agentes:AgenteRolSkill (Habilidad)The Data WardenArquitecto de DatosOperaciones CRUD directas con Firestore.The Interface CraftsmanConstructor de UIRenderizado de formularios y listas dinámicas.The Product SpiritModelo de DatosEstructura lógica del objeto Producto.💻 4. Código Totalmente FuncionalA. El Modelo (The Product Spirit)lib/models/product_model.dartDartclass Product {
  String id;
  String nombre;
  double precio;
  int stock;

  Product({required this.id, required this.nombre, required this.precio, required this.stock});

  Map<String, dynamic> toMap() => {
    "nombre": nombre,
    "precio": precio,
    "stock": stock,
  };
}
B. El Agente: Data Warden (CRUD Skills)lib/agents/data_warden.dartDartimport 'package:cloud_firestore/cloud_firestore.dart';

class DataWarden {
  final CollectionReference _db = FirebaseFirestore.instance.collection('productos');

  // Skill: Crear
  Future<void> create(String n, double p, int s) => _db.add({'nombre': n, 'precio': p, 'stock': s});

  // Skill: Leer (Stream)
  Stream<QuerySnapshot> read() => _db.snapshots();

  // Skill: Actualizar
  Future<void> update(String id, String n, double p, int s) => 
      _db.doc(id).update({'nombre': n, 'precio': p, 'stock': s});

  // Skill: Borrar
  Future<void> delete(String id) => _db.doc(id).delete();
}
C. La UI: Interface Craftsmanlib/ui/inventory_screen.dartDartimport 'package:flutter/material.dart';
import '../agents/data_warden.dart';
import 'package:cloud_firestore/cloud_firestore.dart';

class InventoryScreen extends StatelessWidget {
  final DataWarden warden = DataWarden();
  final nameController = TextEditingController();
  final priceController = TextEditingController();
  final stockController = TextEditingController();

  void _showForm(BuildContext context, {String? id}) {
    showModalBottomSheet(
      context: context,
      isScrollControlled: true,
      builder: (_) => Padding(
        padding: EdgeInsets.only(bottom: MediaQuery.of(context).viewInsets.bottom, left: 20, right: 20, top: 20),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            TextField(controller: nameController, decoration: const InputDecoration(labelText: 'Nombre')),
            TextField(controller: priceController, decoration: const InputDecoration(labelText: 'Precio'), keyboardType: TextInputType.number),
            TextField(controller: stockController, decoration: const InputDecoration(labelText: 'Stock'), keyboardType: TextInputType.number),
            const SizedBox(height: 20),
            ElevatedButton(
              onPressed: () {
                if (id == null) {
                  warden.create(nameController.text, double.parse(priceController.text), int.parse(stockController.text));
                } else {
                  warden.update(id, nameController.text, double.parse(priceController.text), int.parse(stockController.text));
                }
                Navigator.pop(context);
              },
              child: Text(id == null ? 'Agregar Producto' : 'Actualizar'),
            )
          ],
        ),
      ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('ALABURGER Inventory')),
      body: StreamBuilder(
        stream: warden.read(),
        builder: (context, AsyncSnapshot<QuerySnapshot> snapshot) {
          if (!snapshot.hasData) return const Center(child: CircularProgressIndicator());
          return ListView(
            children: snapshot.data!.docs.map((doc) => ListTile(
              title: Text(doc['nombre']),
              subtitle: Text('Precio: \$${doc['precio']} | Stock: ${doc['stock']}'),
              trailing: Row(
                mainAxisSize: MainAxisSize.min,
                children: [
                  IconButton(icon: const Icon(Icons.edit, color: Colors.orange), onPressed: () => _showForm(context, id: doc.id)),
                  IconButton(icon: const Icon(Icons.delete, color: Colors.red), onPressed: () => warden.delete(doc.id)),
                ],
              ),
            )).toList(),
          );
        },
      ),
      floatingActionButton: FloatingActionButton(onPressed: () => _showForm(context), child: const Icon(Icons.add)),
    );
  }
}
D. Main (Punto de Partida)lib/main.dartDartimport 'package:flutter/material.dart';
import 'package:firebase_core/firebase_core.dart';
import 'ui/inventory_screen.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(); // Fuego inicial
  runApp(MaterialApp(
    theme: ThemeData(primarySwatch: Colors.orange),
    home: InventoryScreen(),
  ));
}
🎓 5. Metodología de Flujo de Trabajo (Para Estudiantes)Definir el Agente: Antes de programar, el estudiante debe decidir qué agente se encarga de qué.Entrenar el Skill: Escribir la función específica (ej. delete).Desplegar el Rol: Conectar la UI con el agente.Sincronización: Verificar en tiempo real en la consola de Firebase que los datos de nombre, precio y stock fluyan correctamente.¡Este proyecto está listo para ser servido! Directo "Al Burger" y totalmente funcional. 🚀🍔
