# BaseViewModel

    class BaseViewModel : ViewModel(){
        private val disposable = CompositeDisposable()

        private val _state = BehaviorSubject.create<BaseState>()

        val state = _state.hide()

        init {
            _state.onNext(BaseState())
        }

        private fun handleEvents(vararg events: BaseState.Event) {
            events.forEach { event ->
                _state
                    .value!!
                    .reduce(event)
                    .let {
                        _state.onNext(it)
                    }
            }
        }

        fun resetEffect() {
            handleEvents(BaseState.Event.ResetEffect)
        }

        override fun onCleared() {
            super.onCleared()
            disposable.clear()
        }
    }
    //
    
data class BaseState(
    val selectedMenu: Menu = Menu.Main,
    val effect: Effect = Effect.EmptyEffect
) {


    sealed class Event {
        object ResetEffect : Event()
    }

    sealed class Effect {
        object EmptyEffect : Effect()
    }

    fun reduce(event: Event): BaseState {
        return when (event) {
            Event.ResetEffect -> copy(effect = Effect.EmptyEffect)
            else -> this
        }
    }
}
