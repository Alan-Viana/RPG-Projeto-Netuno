const readlineSync = require('readline-sync');

let jogador = {
    nome: '',
    email: '',
    senha: '',
    confirmarSenha: '',
    idade: 0,
    diaNascimento: 0,
    mesNascimento: 0,
    anoNascimento: 0,
    genero: '',
    classe: '',
    arma: '',
    corCabelo: '',
    corPele: '',
    montaria: '',
    pontosDisponiveis: 50,
    forca: 0,
    precisao: 0,
    destreza: 0,
    mana: 0,
    vida: 0,
    velocidadeMontaria: 0,
    tempoDescansoMontaria: 0,
    resistenciaMontaria: 0
};

function validarNome() {
    const regex = /^[a-zA-Z\u00C0-\u00FF ]+$/;
    jogador.nome = readlineSync.question("Nome completo: ");
    return regex.test(jogador.nome) && jogador.nome.trim().split(/\s+/).length >= 2;
}

function validarDataNascimento() {
    let dataNascimento = readlineSync.question("Data de nascimento (DDMMAAAA): ");
    if (dataNascimento.length !== 8) return false;

    jogador.diaNascimento = parseInt(dataNascimento.substring(0, 2));
    jogador.mesNascimento = parseInt(dataNascimento.substring(2, 4));
    jogador.anoNascimento = parseInt(dataNascimento.substring(4, 8));

    if (isNaN(jogador.diaNascimento) || isNaN(jogador.mesNascimento) || isNaN(jogador.anoNascimento) || jogador.anoNascimento < 1900 || jogador.anoNascimento > new Date().getFullYear()) {
        return false;
    }

    let dataNascimentoValida = new Date(jogador.anoNascimento, jogador.mesNascimento - 1, jogador.diaNascimento);
    jogador.idade = calcularIdade(dataNascimentoValida);
    return jogador.idade >= 18;
}

function calcularIdade(dataNascimento) {
    let hoje = new Date();
    let idade = hoje.getFullYear() - dataNascimento.getFullYear();
    let mesAtual = hoje.getMonth();
    let diaAtual = hoje.getDate();

    if (mesAtual < dataNascimento.getMonth() || (mesAtual === dataNascimento.getMonth() && diaAtual < dataNascimento.getDate())) {
        idade--;
    }
    return idade;
}

function escolherOpcao(opcoes, mensagem) {
    let escolha;
    do {
        console.log(mensagem);
        opcoes.forEach((opcao, index) => console.log(`${index + 1}) ${opcao}`));
        escolha = parseInt(readlineSync.question(`Escolha (1 a ${opcoes.length}): `));
    } while (isNaN(escolha) || escolha < 1 || escolha > opcoes.length);
    return opcoes[escolha - 1];
}

async function printComDelay() {
    const imprimirComAtraso = async (mensagem, delay = 500) => {
        await new Promise(resolve => setTimeout(resolve, delay));
        console.log(mensagem);
    };

    await imprimirComAtraso("\n---------- Dados do Jogador ----------");
    await imprimirComAtraso(`Nome: ${jogador.nome}`);
    await imprimirComAtraso(`Email: ${jogador.email}`);
    await imprimirComAtraso(`Idade: ${jogador.idade}`);
    await imprimirComAtraso(`Gênero: ${jogador.genero}`);
    await imprimirComAtraso(`Classe: ${jogador.classe}`);
    await imprimirComAtraso(`Arma: ${jogador.arma}`);
    await imprimirComAtraso(`Cor de cabelo: ${jogador.corCabelo}`);
    await imprimirComAtraso(`Cor de pele: ${jogador.corPele}`);
    await imprimirComAtraso(`Montaria: ${jogador.montaria}`);
    await imprimirComAtraso("\n---------- Atributos ----------");
    await imprimirComAtraso(`Força: ${jogador.forca}`);
    await imprimirComAtraso(`Precisão: ${jogador.precisao}`);
    await imprimirComAtraso(`Destreza: ${jogador.destreza}`);
    await imprimirComAtraso(`Mana: ${jogador.mana}`);
    await imprimirComAtraso(`Vida: ${jogador.vida}`);
    await imprimirComAtraso("\n---------- Atributos da Montaria ----------");
    await imprimirComAtraso(`Velocidade: ${jogador.velocidadeMontaria}`);
    await imprimirComAtraso(`Tempo de descanso: ${jogador.tempoDescansoMontaria} min`);
    await imprimirComAtraso(`Resistência: ${jogador.resistenciaMontaria}`);

    readlineSync.keyInPause("\nPressione qualquer tecla para sair...");
}

async function main() {
    console.log("---------- Registre-se ----------");

    while (!validarNome()) {
        console.log("Por favor, insira seu nome completo usando apenas letras e espaços!");
    }

    while (!validarDataNascimento()) {
        console.log("Data de nascimento inválida, você deve ter 18 anos ou mais para se registrar!.");
    }

    console.log("\n---------- Informações de Contato ----------");
    do {
        jogador.email = readlineSync.question("Email: ");
        if (!/^\S+@\S+\.\S+$/.test(jogador.email)) {
            console.log("Formato de email inválido. Use um formato como exemplo@email.com");
        }
    } while (!/^\S+@\S+\.\S+$/.test(jogador.email));

    console.log("\n---------- Criar Senha ----------");
    do {
        jogador.senha = readlineSync.question("Senha: ", { hideEchoBack: true });
        jogador.confirmarSenha = readlineSync.question("Confirme sua Senha: ", { hideEchoBack: true });
        if (jogador.senha !== jogador.confirmarSenha) {
            console.log("As senhas não correspondem. Tente novamente.");
        }
    } while (jogador.senha !== jogador.confirmarSenha);

    let podeRegistrar = true;

    if (podeRegistrar) {
        console.log("\nRegistro concluído com sucesso!");

        console.log("\n---------- Login ----------");
        let tentativas = 0;
        let loginEmail, loginSenha;

        do {
            loginEmail = readlineSync.question("Digite seu e-mail: ");
            loginSenha = readlineSync.question("Digite sua senha: ", { hideEchoBack: true });

            if (loginEmail === jogador.email && loginSenha === jogador.senha) {
                console.log("Login bem sucedido!");
                console.log("\n---------- Início do Jogo ----------");
                break;
            } else {
                console.log("E-mail ou senha inválidos! Tente novamente.");
                tentativas += 1;

                if (tentativas >= 3) {
                    console.log("Você excedeu o limite de tentativas. O programa será encerrado.");
                    process.exit();
                }
            }
        } while (true);

        const generos = ["Masculino", "Feminino"];
        jogador.genero = escolherOpcao(generos, "Gênero (Masculino, Feminino):");

        const classes = jogador.genero === "Masculino" ?
            ["Paladino", "Atirador", "Guerreiro", "Bárbaro", "Arqueiro"] :
            ["Paladina", "Atiradora", "Guerreira", "Bárbara", "Arqueira"];

        jogador.classe = escolherOpcao(classes, `Escolha a classe para jogar (${classes.join(', ')}):`);

        switch (jogador.classe) {
            case "Paladino":
            case "Paladina":
                jogador.arma = escolherOpcao(["Cajado", "Alabardas"], "Escolha a arma para o Paladino:");
                distribuirPontos(7, 10, 8, 15, 10);
                break;
            case "Atirador":
            case "Atiradora":
                jogador.arma = escolherOpcao(["Rifle", "Pistolas Duplas"], "Escolha a arma para o Atirador:");
                distribuirPontos(7, 19, 16, 0, 8);
                break;
            case "Guerreiro":
            case "Guerreira":
                jogador.arma = escolherOpcao(["Lança", "Espada e Escudo"], "Escolha a arma para o Guerreiro:");
                distribuirPontos(15, 5, 10, 0, 20);
                break;
            case "Bárbaro":
            case "Bárbara":
                jogador.arma = escolherOpcao(["Espada Longa", "Machado"], "Escolha a arma para o Bárbaro:");
                distribuirPontos(20, 3, 6, 0, 21);
                break;
            case "Arqueiro":
            case "Arqueira":
                jogador.arma = escolherOpcao(["Arco", "Besta"], "Escolha a arma para o Arqueiro:");
                distribuirPontos(3, 20, 10, 5, 12);
                break;
            default:
                console.log("Escolha inválida.");
                process.exit();
        }

        const coresCabelo = ["Preto", "Castanho Escuro", "Castanho Claro", "Loiro", "Ruivo", "Branco"];
        jogador.corCabelo = escolherOpcao(coresCabelo, "Cor de cabelo (Preto, Castanho Escuro, Castanho Claro, Loiro, Ruivo, Branco):");

        const coresPele = jogador.genero === "Masculino" ?
            ["Preto", "Pardo", "Branco", "Indígena", "Amarelo"] :
            ["Preta", "Parda", "Branca", "Indígena", "Amarela"];

        jogador.corPele = escolherOpcao(coresPele, `Cor de pele (${coresPele.join(', ')}):`);

        const montarias = ["Panda", "Cavalo", "Tigre", "Elefante", "Lobo"];
        jogador.montaria = escolherOpcao(montarias, "Escolha sua Montaria:");

        switch (jogador.montaria) {
            case "Panda":
                distribuirPontosMontaria(3, 20, 10);
                break;
            case "Cavalo":
                distribuirPontosMontaria(7, 10, 6);
                break;
            case "Tigre":
                distribuirPontosMontaria(8, 5, 12);
                break;
            case "Elefante":
                distribuirPontosMontaria(4, 12, 15);
                break;
            case "Lobo":
                distribuirPontosMontaria(9, 5, 11);
                break;
            default:
                console.log("Escolha de montaria inválida.");
                process.exit();
        }

        await printComDelay();
    }
}

function distribuirPontos(forca, precisao, destreza, mana, vida) {
    jogador.forca = forca;
    jogador.precisao = precisao;
    jogador.destreza = destreza;
    jogador.mana = mana;
    jogador.vida = vida;
}

function distribuirPontosMontaria(velocidade, descanso, resistencia) {
    jogador.velocidadeMontaria = velocidade;
    jogador.tempoDescansoMontaria = descanso;
    jogador.resistenciaMontaria = resistencia;
}

main();
