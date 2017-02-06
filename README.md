# Projeto-FarmTenp
Game criado para a 2° gamejam na Fatenp (11/11 a 15/11/2016)

>>PLAYER<<

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
