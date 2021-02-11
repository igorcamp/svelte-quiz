<script>
	function shuffleList(l) {
		return l.map((a) => ({sort: Math.random(), value: a})).sort((a, b) => a.sort - b.sort).map((a) => a.value);
	}

	let questions = [
	];

	fetch('./questions.json').then(d => {
		return d.json();
	}).then(d => {
		questions = d;
		step = 0;
	});

	questions = shuffleList(questions);

	let selected_questions = null;

	let step = -1;
	let showAnswer = false;

	let number_of_questions = 1;

	let score = 0;

	let question_number = 0;
	let question = {};
	let answer = null;

	function startQuiz() {
		selected_questions = questions.slice(0, number_of_questions);
		question = selected_questions[0];
		question.answers = shuffleList(question.answers);
		step += 1;
	}

	function verifyAnswer() {
		if (answer === true) {
			score += 1;
		}
		showAnswer = true;
	}

	function nextAnswer() {
		showAnswer = false;
		answer = null;
		if (question_number + 1 < number_of_questions) {
			question_number += 1;
			question = selected_questions[question_number];
			question.answers = shuffleList(question.answers);
		} else {
			finishQuiz();
		}
	}

	function finishQuiz() {
		step = 2;
	}
</script>

<style>
	.question-number {
		font-size: 10px;
		color: gray;
	}
	.question {
		margin: 10px;
	}

	.question-text {
		font-weight: bold;
		margin-bottom: 20px;
	}

	.answer {
		width: 100%;
		margin: 10px 0px;
		padding: 10px;
	}

	.correct-answer {
		background: green;
		color: white;
	}

	.wrong-answer {
		background: red;
		color: white;
	}

	button {
		font-size: 18px;
		padding: 10px;
	}
</style>

<main>
	{#if step == -1}
		<span>Carregando perguntas</span>
	{/if}

	{#if step == 0 }
		<div>
			Quantas perguntas quer responder? <br>
			<input type="number" min="1" max={questions.length} bind:value={number_of_questions}>
			<button on:click={startQuiz}>Iniciar!</button>
		</div>
	{/if}

	{#if step == 1}
	<div class="question-number">Pergunta {question_number + 1} de {number_of_questions}</div>
	<div class="question">
		<div class="question-text">{question.text}</div>

		{#each question.answers as a, i}
			<label class="answer" 
				class:correct-answer="{showAnswer && a.correct}"
				class:wrong-answer="{showAnswer && answer === "false" + i}">
				<input type="radio" value={a.correct || "false" + i} bind:group={answer} disabled={showAnswer}>
				{a.text}
			</label>
		{/each}
		
		{#if !showAnswer}
			<button on:click={verifyAnswer} disabled={answer === null}>Confirmar</button>	
		{/if}
		{#if showAnswer}
			{#if question_number + 1 < number_of_questions}
				<button on:click={nextAnswer}>Próxima pergunta</button>
			{:else}
				<button on:click={finishQuiz}>Finalizar</button>	
			{/if}
		{/if}
	</div>
	{/if}

	{#if step == 2}
		<div class="results">
			Você acertou {score} de {number_of_questions} perguntas.
			<br>
			<button onclick="window.location.reload(false);">Fazer outro teste</button>
		</div>
	{/if}
</main>