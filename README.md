# X5 SDK Integration

For implementing SDK you need to integrate dependency into your project. (Gradle link)
## Preface
- SDK Initialization ✨
- SDK Features ✨

## SDK Initialization

For using SDK you need to initialization it. 
You need to add the following code: 
>    with(SDKConfigurator) { </br>
>            initKoin(this@tYourClass) </br>
>            initCalligraphy()</br> 
>            initVuforia() </br>
>        }</br>


## SDK Features.

1. For using Backend requests you need to provide USER_TOKEN to NetworkConfigurator.

>   NetworkConfigurator.Configurator() </br>
>           .token(USER_TOKEN) </br>
>            .configure() </br>

USER_TOKEN - for example it's your Bearer Authourization token.

2. For using UI part. You can use **GameSDK, ConverterSDK, StoreSDK, ProductSDK**
   Each SDK includes screens and functions that perform one business logic.

  a. ConverterSDK, StoreSDK, ProductSDK has only one method for call:
  
> startActivity(StoreSDK.storeScreen().makeIntent(this)) </br>
> startActivity(ConverterSDK.converterScreen().makeIntent(this)) </br>
> startActivity(ProductSDK.productSreen().makeIntent(this)) </br>

  b. GameSDK unclude this methods: aloneGameScreen, fullGameScreen and  checkGamePoints.
  
> aloneGameScreen( latitude: Double,longitude: Double, winPoints: Int ) - can open Game with parameters shop coordinates' & game points. </br>
> </br>
> fullGameScreen() - can open PreGameDasboard, Timer & Game screens.

 c. The game has one problem, that you need to sync 'win points', not in game flow, because can happen that the user has a problem with the Internet, but he won in game and can't sync data with Backend. And for this in GameSDK has two solutions
- First, has method **checkGamePoints** where you can handle game points in your app, but need to handle Rx subscription or add Rx to your project.
- Second case, GameSDK has class **GamePointsObserver** that can subscribe to Lifecycle and you don't need to do anything.

## Example Activity implementation



    private val disposable = CompositeDisposable()

    /* TODO [DATA]  */
    private val USER_TOKEN =
        "JWT eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjo0LCJ1c2VybmFtZSI6IkFsZXhlX1Jva2FsbyIsImV4cCI6MTYyMDA4NjU3MSwiZW1haWwiOiJtci5yb2thbG9AbWFpbC5ydSJ9.SIgb_TJFLqfbLaBgMQe6qbikW4cfZDmwWD52XX3LSF0"

    private val gamePointsObserver = GamePointsObserver()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        NetworkConfigurator.Configurator()
            .token(USER_TOKEN)
            .configure()

        lifecycle.addObserver(gamePointsObserver)

        btnGameAlone.setOnClickListener {
            startActivity(GameSDK.aloneGameScreen(0.0, 0.0, 100).makeIntent(this))
        }

        btnFullGame.setOnClickListener {
            startActivity(GameSDK.fullGameScreen().makeIntent(this))
        }

        btnConvert.setOnClickListener {
            startActivity(ConverterSDK.converterScreen().makeIntent(this))
        }

        btnStore.setOnClickListener {
            startActivity(StoreSDK.storeScreen().makeIntent(this))
        }

        btnProduct.setOnClickListener {
            startActivity(ProductSDK.productSreen().makeIntent(this))
        }
    }

    override fun onResume() {
        super.onResume()
        /* TODO [EXAMPLE] Another variant */
       disposable.add(
           GameSDK.checkGamePoints()
                .subscribe()
        )
    }

    override fun onDestroy() {
        super.onDestroy()
        /* TODO [EXAMPLE] Another variant */
        disposable.clear()
        lifecycle.removeObserver(gamePointsObserver)
    }

