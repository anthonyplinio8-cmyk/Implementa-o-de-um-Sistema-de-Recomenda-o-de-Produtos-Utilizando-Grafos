# Implementa-o-de-um-Sistema-de-Recomenda-o-de-Produtos-Utilizando-Grafos
[Usuário]
   ↓
[API de Busca]
   ↓
[Index HashMap] ←→ [Grafo de Recomendação]
   ↓
[Resultados]
3. IMPLEMENTAÇÃO EM RUST
use std::collections::{HashMap, HashSet};

#[derive(Debug, Clone)]
struct Product {
    id: u32,
    name: String,
    category: String,
}

struct SearchEngine {
    index: HashMap<String, Vec<Product>>,
    graph: HashMap<u32, HashSet<u32>>, // recomendações
}

impl SearchEngine {
    fn new() -> Self {
        Self {
            index: HashMap::new(),
            graph: HashMap::new(),
        }
    }

    fn add_product(&mut self, product: Product) {
        self.index
            .entry(product.name.to_lowercase())
            .or_insert(Vec::new())
            .push(product.clone());
    }

    fn search(&self, query: &str) -> Vec<Product> {
        self.index
            .get(&query.to_lowercase())
            .cloned()
            .unwrap_or(Vec::new())
    }

    fn add_relation(&mut self, p1: u32, p2: u32) {
        self.graph.entry(p1).or_insert(HashSet::new()).insert(p2);
        self.graph.entry(p2).or_insert(HashSet::new()).insert(p1);
    }

    fn recommend(&self, product_id: u32) -> Vec<u32> {
        self.graph
            .get(&product_id)
            .map(|s| s.iter().cloned().collect())
            .unwrap_or(Vec::new())
    }
}

fn main() {
    let mut engine = SearchEngine::new();

    let p1 = Product { id: 1, name: "Notebook".into(), category: "Eletrônicos".into() };
    let p2 = Product { id: 2, name: "Mouse".into(), category: "Eletrônicos".into() };

    engine.add_product(p1.clone());
    engine.add_product(p2.clone());

    engine.add_relation(1, 2);

    println!("Busca: {:?}", engine.search("Notebook"));
    println!("Recomendados: {:?}", engine.recommend(1));
}
