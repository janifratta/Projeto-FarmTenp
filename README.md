# Projeto-FarmTenp
Game criado para a 2° gamejam na Fatenp (11/11 a 15/11/2016)

>>PLAYER

public class Player : MonoBehaviour {

    //fisica unity
    Rigidbody2D rb;

    //mudar velocidade
    public float velocity;

    //funcao para acessar a animação
    Animator anim;

	void Start () {
        //inserir o script nas fisicas
        rb = GetComponent<Rigidbody2D>();
	}
	void Update () {
        //movimentar o fazendeiro
        rb.velocity = new Vector2(
            Input.GetAxis("Horizontal") * velocity,
            Input.GetAxis("Vertical") * velocity);
	}
}

>>PLAYERMOVIMENTOS

public class PlayerMovement : MonoBehaviour {

    public float speed;

	void Update () {
        // Verificar se o jogo está ativo
        if (GameManager.ativo) {
            if (Input.GetKey(KeyCode.D))
            {
                transform.Translate(Vector2.right * speed);
            }
            if (Input.GetKey(KeyCode.A))
            {
                transform.Translate(-Vector2.right * speed);
            }
            if (Input.GetKey(KeyCode.W))
            {
                transform.Translate(Vector2.up * speed);
            }
            if (Input.GetKey(KeyCode.S))
            {
                transform.Translate(-Vector2.up * speed);
            }
        }
    }
}

>>PLAYERANIMAÇÕES

public class PlayerAnimation : MonoBehaviour
{
    public Animator anim;

    void Start()
    {
        anim = GetComponent<Animator>();
    }

    void Update()
    {
        // Verificar se o jogo está ativo
        if (GameManager.ativo)
        {
            anim.SetBool("andarEsquerda", Input.GetKey(KeyCode.A));
            anim.SetBool("andarDireita", Input.GetKey(KeyCode.D));
            anim.SetBool("andarCima", Input.GetKey(KeyCode.W));
            anim.SetBool("andarBaixo", Input.GetKey(KeyCode.S));
        }
    }
}

>>INVESTIDORES

public class Investidor : MonoBehaviour {

    string[] invs = { "João", "Garibado", "Eurico" };
    public static int invSelecionado;

    // Será acessado externamente
    public static string investidor;

    void Start () {
        // Controle do investidor
        // 1 - Organico; 2 - Industrial; 3 - Misto
        invSelecionado = Random.Range(0, 3);

        // Guardar o investidor
        investidor = invs[invSelecionado];
    }
}

>>GAMEMANAGER

public class GameManager : MonoBehaviour
{
    public static int periodo;

    // Talvez não sirva pra nada
    public static int pontuacaoDia;

    // Ivestidor
    string[] invs = { "João", "Garibado", "Eurico" };
    string[] invsText = { "Industrial", "Misto", "Natural" };
    public static int invSelecionado;

    // Será acessado externamente
    public static string investidor;

    // painel botoes
    public static GameObject pBotoes;

    // Controlar a atividade do jogo
    public static bool ativo;

    public Sprite[] spritesInvs;

    public Image imageInvestidor;

    public Text termo;

    void Start()
    {
        pBotoes = GameObject.Find("PBotoes");
        
        // Controle do investidor
        invSelecionado = Random.Range(0, 3);

        // Guardar o investidor
        investidor = invs[invSelecionado];

        // Trocar a imagem do investidor
        imageInvestidor.sprite = spritesInvs[invSelecionado];

        // Mostrar o texto
        termo.text = "O investiror gostaria de produtos " + invsText[invSelecionado];

        Debug.Log("Investidor selecionado " + investidor);
    }

    // Iniciar o jogo
    public void Iniciar()
    {
        ativo = true;
        pBotoes.SetActive(false);
        imageInvestidor.gameObject.SetActive(false);
    }

    void Update()
    {
        if (periodo == 4 && pontuacaoDia == 0)
        {
            // está escrito aqui que estou no dia final e que só usei produtos naturais.
        }
        else if (periodo == 4 && pontuacaoDia == 18)
        {
            // está escrito aqui que estou no dia final e que só usei produtos industriais.
        }
        else if (periodo == 4 && pontuacaoDia >= 1 && pontuacaoDia <= 17)
        {
            // está escrito aqui que estou no dia final e que só usei ambos produtos, indiferente da composição.
        }
    }
    // Utilizar este método para contabiliar os pontos
    private static void SomarPontos()
    {
        // Somar todos os pontos da VETOR  pontos
        int sum = pontuacaoDia;
  
        //Primeiro investidor, totalmente industrial
        if (invSelecionado == 0)
        {
            if (sum == 18)
            {
                Application.LoadLevel("ContratoFechado");
            }
            else if (sum <= 17)
            {
                Application.LoadLevel("GameOver");
            }
        }

        //Segundo investidor, quer crops diversos
        else if (invSelecionado == 1)
        {
            if (sum >= 1 && sum <= 17)
            {
                Application.LoadLevel("ContratoFechado");
            }
            else if (sum == 0 || sum == 18)
            {
                Application.LoadLevel("GameOver");
            }
        }

        //Terceiro investidor, totalmente natural
        else if (invSelecionado == 2)
        {
            if (sum == 0)
            {
                Application.LoadLevel("ContratoFechado");
            }
            else if (sum >= 1)
            {
                Application.LoadLevel("GameOver");
            }
        }
    }

    void OnTriggerStay2D(Collider2D coli)
    {
        if (Input.GetKeyDown(KeyCode.C))
        {
            periodo = periodo + 1;
        }
        
    }

    // FAZER CODIGO PARA GUARDAR OS DADOS DO DIA 1 E RESETAR OS TERRENOS E PUXAR A CENA DO DIA 2
    public static bool VerificarTerrenos()
    {
        //vasculhar todos os terrenos
        GameObject[] terrenos = GameObject.FindGameObjectsWithTag("Terreno");
        for (int i = 0; i < terrenos.Length; i++)
        {
            // Selecionando um terreno
            Terreno terreno = terrenos[i].GetComponent<Terreno>();
            if (terreno.tipoItem == -1)
            {
                return false;
            }
        }
        return true;
    }

    public static void AvancarPeriodo()
    {
        // primeiro, verificar os terrenos
        if (VerificarTerrenos())
        {
            //vasculhar todos os terrenos
            GameObject[] terrenos = GameObject.FindGameObjectsWithTag("Terreno");
            for (int i = 0; i < terrenos.Length; i++)
            {
                // Selecionando um terreno
                Terreno terreno = terrenos[i].GetComponent<Terreno>();
                // Resetar o terrebi para reiniciar a cena
                terreno.tipoItem = -1;
            }
            Debug.Log("Pontuação atual: " + pontuacaoDia + " dia " + periodo);

            //avancar o dia / período
            periodo++;

            // Verificar a condição de parada do jogo
            if (periodo > 2)
            {
                SomarPontos();
            }
        }
    }
}

>>TERRENOS

public class Terreno : MonoBehaviour
{
    // -1 para nada; 0 organico; 1 industrial
    public string termo;
    public bool controle;
    public GameObject ui;
    public int tipoItem = -1;
    public Text textinho;

    void Update()
    {

        if (controle && tipoItem == -1)
        {
            //retorna valor boleano 
            if (Input.GetKey(KeyCode.Z))
            {
                Debug.Log("Apertou z");

                tipoItem = 0;
                GameManager.pBotoes.SetActive(false);
            }
            if (Input.GetKey(KeyCode.X))
            {
                tipoItem = 1;
                GameManager.pontuacaoDia += 1;
                GameManager.pBotoes.SetActive(false);
            }
        }
    }
    void OnTriggerStay2D(Collider2D coll)
    {
        if (coll.tag == "Player")
        {
            textinho.text = termo;
        }
    }
    void OnTriggerEnter2D(Collider2D other)
    {
        if (tipoItem == -1)
        {
            controle = true;
            GameManager.pBotoes.SetActive(true);
        }
    }
    void OnTriggerExit2D(Collider2D other)
    {
        GameManager.pBotoes.SetActive(false);
        controle = false;
    }
}

>>PORTA

public class Portinha : MonoBehaviour
{

    public string termo;
    public GameObject ui;
    public int tipoItem = -1;
    public Text textinho;

    void OnTriggerStay2D(Collider2D coll)
    {
        if (coll.tag == "Player")
        {
            textinho.text = termo;
        }
    }

    void OnTriggerExit2D(Collider2D other)
    {
        GameManager.pBotoes.SetActive(false);
    }
    void OnTriggerEnter2D(Collider2D other)
    {
        if (!GameManager.VerificarTerrenos())
        {
            Debug.Log("Incompeto");
            GameManager.pBotoes.SetActive(true);
        }
        else
        {
            // Avançar o período
            GameManager.AvancarPeriodo();
        }
    }
}

