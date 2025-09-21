<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Controle de Estoque</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
        }
    </style>
</head>
<body class="bg-gray-100 min-h-screen p-4 md:p-8">
    <div class="max-w-7xl mx-auto">
        <!-- Header -->
        <header class="flex flex-col md:flex-row justify-between items-center bg-white p-6 rounded-3xl shadow-lg mb-8">
            <h1 class="text-3xl md:text-4xl font-extrabold text-gray-800">Controle de Estoque</h1>
            <div class="mt-4 md:mt-0 text-sm text-gray-500 font-medium text-center md:text-right">
                <p class="font-semibold text-gray-800">Modo de Visualização - Dados do CSV</p>
                <p>As funcionalidades de edição estão desativadas.</p>
            </div>
        </header>

        <!-- Main Content Grid -->
        <main class="grid grid-cols-1 lg:grid-cols-3 gap-8">
            <!-- Coluna 1: Listas de Dados -->
            <div class="lg:col-span-2 space-y-8">
                <!-- Seção: Itens em Estoque -->
                <div class="bg-white p-6 rounded-3xl shadow-lg">
                    <h2 class="text-2xl font-bold text-gray-700 mb-4">Itens em Estoque</h2>
                    <div id="loading" class="text-center py-8">
                        <div class="animate-spin rounded-full h-12 w-12 border-4 border-t-blue-500 mx-auto"></div>
                        <p class="mt-2 text-gray-500">Carregando dados da planilha...</p>
                    </div>
                    <div id="inventory-list" class="grid gap-4">
                        <!-- Cards de produtos serão inseridos aqui pelo JavaScript -->
                    </div>
                </div>
            </div>

            <!-- Coluna 2: Formulários Desativados -->
            <div class="space-y-8">
                <!-- Seção de Registro de Uso (Materiais) -->
                <div class="bg-white p-6 rounded-3xl shadow-lg opacity-50 cursor-not-allowed">
                    <h2 class="text-2xl font-bold text-gray-700 mb-4">Registrar Uso (Materiais)</h2>
                    <div class="text-center text-gray-500 font-medium py-10">
                        <p>Função desativada neste modo.</p>
                    </div>
                </div>

                <!-- Seção de Adicionar Novo Produto (Materiais) -->
                <div class="bg-white p-6 rounded-3xl shadow-lg opacity-50 cursor-not-allowed">
                    <h2 class="text-2xl font-bold text-gray-700 mb-4">Adicionar/Atualizar Produto</h2>
                    <div class="text-center text-gray-500 font-medium py-10">
                        <p>Função desativada neste modo.</p>
                    </div>
                </div>

                 <!-- Seção: Adicionar Ferramenta -->
                <div class="bg-white p-6 rounded-3xl shadow-lg opacity-50 cursor-not-allowed">
                    <h2 class="text-2xl font-bold text-gray-700 mb-4">Adicionar Ferramenta</h2>
                    <div class="text-center text-gray-500 font-medium py-10">
                        <p>Função desativada neste modo.</p>
                    </div>
                </div>
            </div>
        </main>
    </div>

    <!-- JavaScript para carregar e renderizar os dados do CSV -->
    <script>
        const inventoryListEl = document.getElementById('inventory-list');
        const loadingEl = document.getElementById('loading');

        const csvFile = 'Controle de Estoque - Folha4.csv';

        // Função para converter CSV em um array de objetos
        function parseCSV(text) {
            const lines = text.trim().split('\n');
            const headers = lines[0].split(',').map(header => header.trim());
            const data = [];
            for (let i = 1; i < lines.length; i++) {
                const values = lines[i].split(',').map(value => value.trim());
                if (values.length === headers.length) {
                    const item = {};
                    headers.forEach((header, index) => {
                        item[header] = values[index];
                    });
                    data.push(item);
                }
            }
            return data;
        }

        // Renderiza a lista de estoque com os dados do CSV
        function renderInventoryList(products) {
            inventoryListEl.innerHTML = '';
            if (products.length === 0) {
                inventoryListEl.innerHTML = '<p class="text-gray-500 text-center">Nenhum produto encontrado no arquivo CSV.</p>';
                return;
            }

            products.forEach(product => {
                const stockValue = parseInt(product.Stock, 10);
                const statusColor = stockValue > 0 ? 'bg-green-100 text-green-800' : 'bg-red-100 text-red-800';
                const cardHtml = `
                    <div class="bg-gray-50 p-6 rounded-2xl shadow-sm border border-gray-200 flex flex-col md:flex-row justify-between items-start md:items-center">
                        <div class="mb-4 md:mb-0">
                            <div class="font-bold text-lg text-gray-800">${product["Nome do item"] || 'Sem nome'}</div>
                            <div class="text-sm text-gray-500">${product.Tipo || 'Sem tipo'}</div>
                        </div>
                        <div class="text-right">
                            <div class="font-bold text-2xl text-gray-900">${product.Stock}</div>
                            <div class="text-sm font-semibold mt-1 py-1 px-3 rounded-full ${statusColor}">
                                ${stockValue > 0 ? 'Em estoque' : 'Esgotado'}
                            </div>
                        </div>
                    </div>
                `;
                inventoryListEl.innerHTML += cardHtml;
            });
        }

        // Carrega o arquivo CSV e renderiza a página
        window.onload = function() {
            fetch(csvFile)
                .then(response => {
                    if (!response.ok) {
                        throw new Error('Erro ao carregar o arquivo CSV.');
                    }
                    return response.text();
                })
                .then(text => {
                    loadingEl.style.display = 'none';
                    const products = parseCSV(text);
                    renderInventoryList(products);
                })
                .catch(error => {
                    loadingEl.style.display = 'none';
                    inventoryListEl.innerHTML = `<div class="text-center text-red-500 font-bold p-4">Erro: ${error.message}</div>`;
                    console.error('Erro:', error);
                });
        };
    </script>
</body>
</html>
