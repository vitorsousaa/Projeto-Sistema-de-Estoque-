# Projeto-Sistema-de-Estoque-
// Back-end + banco de dados 


//Configuração do Projeto

//Iniciando criando um novo diretório para o projeto
mkdir sistema-estoque-backend
cd sistema-estoque-backend
npm init -y


//Instalando dependencias 
npm install express sequelize sqlite3 cors
npm install sequelize-cli -g

//Configuração do Sequelize e SQLite

//Inicializando 
sequelize init


//Definição dos Modelos

//Criando um modelo para o produto
// models/Product.js

module.exports = (sequelize, DataTypes) => {
  const Product = sequelize.define('Product', {
    name: {
      type: DataTypes.STRING,
      allowNull: false
    },
    quantity: {
      type: DataTypes.INTEGER,
      allowNull: false,
      defaultValue: 0
    }
  });

  return Product;
};


//Configuração de rotas 

// routes/products.js

const express = require('express');
const router = express.Router();
const db = require('../models');

// Rota para listar todos os produtos
router.get('/', async (req, res) => {
  try {
    const products = await db.Product.findAll();
    res.json(products);
  } catch (error) {
    res.status(500).json({ error: 'Erro ao buscar produtos' });
  }
});

// Rota para criar um novo produto
router.post('/', async (req, res) => {
  const { name, quantity } = req.body;
  try {
    const newProduct = await db.Product.create({ name, quantity });
    res.status(201).json(newProduct);
  } catch (error) {
    res.status(400).json({ error: 'Erro ao criar produto' });
  }
});

// Rota para atualizar um produto
router.put('/:id', async (req, res) => {
  const { id } = req.params;
  const { name, quantity } = req.body;
  try {
    const product = await db.Product.findByPk(id);
    if (!product) {
      return res.status(404).json({ error: 'Produto não encontrado' });
    }
    product.name = name;
    product.quantity = quantity;
    await product.save();
    res.json(product);
  } catch (error) {
    res.status(400).json({ error: 'Erro ao atualizar produto' });
  }
});

// Rota para remover um produto
router.delete('/:id', async (req, res) => {
  const { id } = req.params;
  try {
    const product = await db.Product.findByPk(id);
    if (!product) {
      return res.status(404).json({ error: 'Produto não encontrado' });
    }
    await product.destroy();
    res.json({ message: 'Produto removido com sucesso' });
  } catch (error) {
    res.status(400).json({ error: 'Erro ao remover produto' });
  }
});

module.exports = router;


//Criação do servidor Express

// index.js

const express = require('express');
const cors = require('cors');
const bodyParser = require('body-parser');
const productRoutes = require('./routes/products');
const app = express();
const PORT = process.env.PORT || 3000;

app.use(cors());
app.use(bodyParser.json());

app.use('/api/products', productRoutes);

app.listen(PORT, () => {
  console.log(`Servidor rodando na porta ${PORT}`);
});


//Inicializador servidor Back-end 

node index.js




