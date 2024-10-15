<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Анализ текста</title>
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.5.2/css/bootstrap.min.css">
    <style>
        body {
            background-image: url('https://wallpaperaccess.com/full/1750994.jpg');
            background-size: cover;
            background-attachment: fixed;
            font-family: 'Dancing Script', cursive;
        }
        .container {
            max-width: 800px;
            background-color: rgba(255, 255, 255, 0.8);
            padding: 20px;
            border-radius: 10px;
        }
        #results {
            display: none;
        }
        .is-invalid {
            border-color: #dc3545;
            box-shadow: 0 0 0 0.2rem rgba(220, 53, 69, 0.25);
        }
        .header-section {
            text-align: center;
            margin-bottom: 20px;
        }
        .writers-gallery {
            display: flex;
            justify-content: center;
            gap: 15px;
            margin-bottom: 20px;
        }
        .writers-gallery img {
            width: 100px;
            height: 150px;
            object-fit: cover;
            border-radius: 10px;
        }
    </style>
    <link href="https://fonts.googleapis.com/css2?family=Dancing+Script:wght@700&display=swap" rel="stylesheet">
</head>
<body>
    <div class="container mt-5">
        <div class="header-section">
            <h1 class="text-center">Анализ текста</h1>
            <div class="writers-gallery">
                <img src="https://upload.wikimedia.org/wikipedia/commons/4/4b/Leo_Tolstoy%2C_portrait.jpg" alt="Лев Толстой">
                <img src="https://upload.wikimedia.org/wikipedia/commons/1/14/Fyodor_Dostoevsky_portrait.jpg" alt="Фёдор Достоевский">
                <img src="https://upload.wikimedia.org/wikipedia/commons/7/7f/Alexander_Pushkin_by_Kiprensky.jpg" alt="Александр Пушкин">
                <img src="https://upload.wikimedia.org/wikipedia/commons/5/5d/Anton_Chekhov_1898_by_Osip_Braz.jpg" alt="Антон Чехов">
                <img src="https://upload.wikimedia.org/wikipedia/commons/7/79/Jane_Austen_coloured_version.jpg" alt="Джейн Остин">
            </div>
        </div>
        <div class="form-group">
            <label for="styleType">Выберите стиль письма:</label>
            <select id="styleType" class="form-control mb-3">
                <option value="formal">Формальный</option>
                <option value="informal">Неформальный</option>
                <option value="persuasive">Убеждающий</option>
                <option value="narrative">Нарративный</option>
                <option value="descriptive">Описание</option>
                <option value="epic">Эпический</option>
                <option value="dramatic">Драматический</option>
                <option value="satirical">Сатирический</option>
            </select>
            <label for="inputText">Введите текст для анализа:</label>
            <textarea id="inputText" class="form-control" rows="10"></textarea>
        </div>
        <button id="analyzeButton" class="btn btn-primary btn-block">Проанализировать текст</button>
        
        <div id="results" class="mt-5">
            <h3>Результаты анализа</h3>
            <p><strong>Количество грамматических ошибок:</strong> <span id="grammarIssues"></span></p>
            <p><strong>Комментарии по грамматике:</strong></p>
            <ul id="grammarFeedback"></ul>
            <p><strong>Оценка читаемости:</strong> <span id="readabilityScore"></span></p>
            <p><strong>Количество предложений:</strong> <span id="sentencesCount"></span></p>
            <p><strong>Количество слов:</strong> <span id="wordsCount"></span></p>
            <p><strong>Средняя длина предложений:</strong> <span id="avgSentenceLength"></span></p>
            <p><strong>Лексическое разнообразие:</strong> <span id="lexicalDiversity"></span></p>
            <p><strong>Количество пассивных конструкций:</strong> <span id="passiveSentences"></span></p>
            <p><strong>Наиболее часто повторяющиеся слова:</strong></p>
            <ul id="mostCommonWords"></ul>
            <p><strong>Рекомендации по улучшению стиля:</strong></p>
            <ul id="styleFeedback"></ul>
            <p><strong>Персонализированные рекомендации:</strong></p>
            <ul id="personalizedRecommendations"></ul>
        </div>
    </div>

    <!-- Modal для отображения ошибок -->
    <div class="modal fade" id="errorModal" tabindex="-1" role="dialog" aria-labelledby="errorModalLabel" aria-hidden="true">
        <div class="modal-dialog" role="document">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="errorModalLabel">Ошибка</h5>
                    <button type="button" class="close" data-dismiss="modal" aria-label="Close">
                        <span aria-hidden="true">&times;</span>
                    </button>
                </div>
                <div class="modal-body">
                    Произошла ошибка при анализе текста. Пожалуйста, попробуйте позже.
                </div>
                <div class="modal-footer">
                    <button type="button" class="btn btn-secondary" data-dismiss="modal">Закрыть</button>
                </div>
            </div>
        </div>
    </div>

    <script>
        const configurableUrl = window.location.origin;

        document.getElementById('analyzeButton').addEventListener('click', () => analyzeText());
        
        const debounce = (func, delay) => {
            let debounceTimer;
            return function() {
                const context = this;
                const args = arguments;
                clearTimeout(debounceTimer);
                debounceTimer = setTimeout(() => func.apply(context, args), delay);
            };
        };

        document.getElementById('inputText').addEventListener('input', debounce(() => {
            document.getElementById('inputText').classList.remove('is-invalid');
        }, 500));

        function analyzeText() {
            const text = document.getElementById('inputText').value;
            const styleType = document.getElementById('styleType').value;

            if (text.trim() === '') {
                document.getElementById('inputText').classList.add('is-invalid');
                document.getElementById('inputText').focus();
                return;
            }

            // Отправляем текст на сервер для анализа с помощью ИИ
            fetch(configurableUrl + '/analyze', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify({
                    text: text,
                    style_type: styleType
                })
            })
            .then(response => {
                if (!response.ok) {
                    switch (response.status) {
                        case 400:
                            throw new Error('Неверный запрос. Пожалуйста, проверьте введенные данные.');
                        case 500:
                            throw new Error('Ошибка сервера. Пожалуйста, попробуйте позже.');
                        default:
                            throw new Error('Произошла неизвестная ошибка. Код: ' + response.status);
                    }
                }
                return response.json();
            })
            .then(results => {
                // Обновляем результаты на странице
                document.getElementById('grammarIssues').innerText = results.grammar_issues;
                document.getElementById('grammarFeedback').innerHTML = results.grammar_feedback.map(fb => `<li>${fb}</li>`).join('');
                document.getElementById('readabilityScore').innerText = results.readability_score;
                document.getElementById('sentencesCount').innerText = results.sentences_count;
                document.getElementById('wordsCount').innerText = results.words_count;
                document.getElementById('avgSentenceLength').innerText = results.avg_sentence_length;
                document.getElementById('lexicalDiversity').innerText = results.lexical_diversity;
                document.getElementById('passiveSentences').innerText = results.passive_sentences;
                document.getElementById('mostCommonWords').innerHTML = results.most_common_words.map(word => `<li>${word[0]}: ${word[1]} раз(а)</li>`).join('');
                document.getElementById('styleFeedback').innerHTML = results.style_feedback.map(fb => `<li>${fb}</li>`).join('');
                document.getElementById('personalizedRecommendations').innerHTML = results.personalized_recommendations.map(rec => `<li>${rec}</li>`).join('');

                document.getElementById('results').classList.remove('d-none');
            })
            .catch(error => {
                console.error('Ошибка при анализе текста:', error);
                document.querySelector('#errorModal .modal-body').innerText = error.message;
                const errorModal = new bootstrap.Modal(document.getElementById('errorModal'));
                errorModal.show();
            });
        }
    </script>
</body>
</html>
