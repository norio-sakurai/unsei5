# unsei5
運勢アプリ
[運勢５.html](https://github.com/user-attachments/files/23739999/default.html)
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>毎日運勢占い</title>
    <style>
        :root {
            --main: #e91e63;
            --accent: #ff6b9d;
            --bg: #fad0c4;
            --light: #fff3e0;
        }
        * { margin:0; padding:0; box-sizing:border-box; }
        body {
            font-family: 'Helvetica Neue', Arial, sans-serif;
            background: linear-gradient(135deg, #ff9a9e, var(--bg));
            color: #333;
            min-height: 100vh;
            padding: 20px;
            display: flex;
            justify-content: center;
            align-items: flex-start;
        }
        .container {
            background: rgba(255,255,255,0.97);
            padding: 30px;
            border-radius: 24px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.15);
            max-width: 480px;
            width: 100%;
            margin-top: 20px;
            text-align: center;
        }
        h1 {
            color: var(--main);
            font-size: 2.2em;
            margin-bottom: 20px;
            text-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }
        .input-group {
            margin: 22px 0;
            text-align: left;
        }
        label {
            display: block;
            font-weight: bold;
            color: #555;
            margin-bottom: 8px;
            font-size: 1.1em;
        }
        input, select {
            width: 100%;
            padding: 14px;
            border: 2px solid #eee;
            border-radius: 12px;
            font-size: 16px;
            transition: all 0.3s;
        }
        input:focus, select:focus {
            border-color: var(--main);
            box-shadow: 0 0 0 3px rgba(233,30,99,0.2);
            outline: none;
        }
        .btn {
            background: linear-gradient(45deg, var(--main), var(--accent));
            color: white;
            border: none;
            padding: 16px 32px;
            border-radius: 50px;
            font-size: 17px;
            font-weight: bold;
            cursor: pointer;
            margin: 12px 8px;
            box-shadow: 0 5px 15px rgba(233,30,99,0.4);
            transition: all 0.3s;
            min-width: 140px;
        }
        .btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 20px rgba(233,30,99,0.5);
        }
        .btn-reset {
            background: linear-gradient(45deg, #9e9e9e, #bdbdbd);
        }
        .result {
            margin-top: 35px;
            padding: 22px;
            background: #f8f9fa;
            border-radius: 18px;
            border-left: 6px solid var(--main);
            text-align: left;
            display: none;
            animation: fadeIn 0.6s ease;
        }
        @keyframes fadeIn { from {opacity:0; transform:translateY(10px);} to {opacity:1; transform:translateY(0);} }
        .day-title {
            font-size: 1.6em;
            color: var(--main);
            text-align: center;
            font-weight: bold;
            margin: 18px 0 12px;
            padding-bottom: 8px;
            border-bottom: 2px dashed #eee;
        }
        .fortune-item {
            margin: 18px 0;
            padding: 16px;
            background: white;
            border-radius: 14px;
            box-shadow: 0 3px 10px rgba(0,0,0,0.06);
            line-height: 1.75;
            font-size: 15px;
        }
        .fortune-label {
            font-weight: bold;
            color: var(--main);
            display: inline-block;
            width: 75px;
            font-size: 1.1em;
        }
        .stars {
            color: #ffb300;
            font-size: 1.3em;
            margin-left: 8px;
        }
        .quote {
            margin-top: 28px;
            padding: 20px;
            background: linear-gradient(45deg, var(--light), #ffe0b2);
            border-radius: 16px;
            font-style: italic;
            color: #d84315;
            border-left: 5px solid #ff8f00;
            line-height: 1.8;
            font-size: 1.05em;
            text-align: center;
        }
        footer {
            margin-top: 40px;
            font-size: 0.8em;
            color: #999;
        }
        @media (max-width: 480px) {
            .container { padding: 20px; }
            h1 { font-size: 1.8em; }
            .btn { padding: 14px 24px; font-size: 16px; }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>毎日運勢占い</h1>
        
        <div class="input-group">
            <label for="birthday">生年月日</label>
            <input type="date" id="birthday" required>
        </div>
        
        <div class="input-group">
            <label for="bloodtype">血液型</label>
            <select id="bloodtype" required>
                <option value="">選択してください</option>
                <option value="A">A型</option>
                <option value="B">B型</option>
                <option value="O">O型</option>
                <option value="AB">AB型</option>
            </select>
        </div>
        
        <button class="btn" onclick="calculateFortune()">今日・明日の運勢を占う</button>
        <button class="btn btn-reset" onclick="resetData()">リセット</button>
        
        <div id="todayResult" class="result"></div>
        <div id="tomorrowResult" class="result"></div>
        
        <footer>※ エンターテイメント目的です</footer>
    </div>

    <script>
        // 占いデータ（各カテゴリ10個、詳細約150文字）
        const fortuneDB = {
            love: [
                "今日は運命的な出会いが訪れる予感です。街中でふと目が合った人が、実は運命の人かもしれません。笑顔で挨拶してみると、自然と会話が弾みます。夕方以降、カフェや本屋で素敵な人が現れる可能性大。勇気を出して声をかけてみて！夜はロマンチックな雰囲気に包まれ、恋の花が咲くかも。心を開いて、流れに身を任せてください。",
                "片思いの相手との距離が一気に縮まる日。LINEやSNSで軽くメッセージを送ってみましょう。返信が来たら、夜に電話で話すチャンスも！相手のペースを尊重しつつ、自分の気持ちを素直に伝えることが大事。夕食の誘いが成功するかも。焦らず、ゆっくり関係を深めていってください。",
                "恋人との絆がさらに深まる一日。夕食を一緒に作る計画を立ててみて。「いつもありがとう」の一言が心を温めます。夜は映画や音楽を共有して、思い出に残る時間を。相手の小さな変化に気づいてあげると、さらに信頼が深まります。愛は育むもの。今日がその日です。",
                "恋愛運は絶好調！デートのお誘いが舞い込むかも。おしゃれに気合いを入れて出かけましょう。夜はロマンチックな雰囲気に包まれ、星空の下で告白のチャンスも。運命の糸が動き出しています。積極的に行動すれば、幸せな未来が待っています！",
                "少しすれ違いが起きやすい日。相手のペースに合わせることが大事。夜に「ごめんね」と伝えると、すぐに仲直りできます。誤解を解くチャンスは今夜。冷静に話し合えば、関係はさらに強くなります。愛は試練を乗り越えてこそ深まるもの。",
                "新しい恋の予感が漂う一日。友人からの紹介がきっかけになるかも。午後は外出を増やしてチャンスを掴んで。夕方、意外な場所で心惹かれる人に出会う可能性。素直な気持ちで接すれば、良い関係がスタートします。運命は自分次第！",
                "パートナーとのコミュニケーションが鍵の日。意見の違いが出ても、優しく聞き入れて。夜のディナータイムが和解のチャンス。感謝の言葉をたくさん伝えて、絆を強化しましょう。今日の努力が、永遠の愛を築きます。",
                "シングルの人は積極的に！SNSやアプリで新しいつながりが生まれるかも。プロフィールを更新して運気をアップ。夕方以降のメッセージ交換が恋の始まりに。自信を持ってアプローチを。幸せはすぐそこに！",
                "恋のライバル出現の兆し。焦らず自分の魅力を発揮して。相手の好みをリサーチし、デートプランを工夫。夜の電話で心を通わせましょう。誠実さが勝負の鍵。今日の忍耐が、明日の勝利につながります。",
                "穏やかな恋愛日和。パートナーとゆったり過ごすのが吉。散歩やお茶タイムで深い話に花を咲かせて。小さなプレゼントが喜びを倍増。今日の温かさが、関係をさらに安定させます。愛を大切に。"
            ],
            work: [
                "仕事が驚くほどスムーズに進む日。午前中に集中してタスクを片付けると、午後は余裕ができます。上司からの評価もアップ！午後の会議ではあなたの意見が採用されるかも。資料は前日に準備しておくと安心。自信を持って発表してください。今日の努力が未来のキャリアにつながります。",
                "新しいプロジェクトのチャンスが到来！積極的に手を挙げて参加してみて。チームの中心となって活躍できるでしょう。アイデアをメモしておくと、会議で光る発言ができます。同僚との連携も良好。今日の行動が、次の昇進への一歩になります。",
                "努力が実を結ぶ日。午後の会議で褒められるかも。資料は前日に準備しておくと安心。自信を持って発表してください。チームの信頼も得られ、責任ある役割を任される可能性大。今日の頑張りが、来月の評価に直結します。",
                "ミスに注意が必要な一日。メールの送信前には必ず確認を。慎重さが運気を安定させます。午前中のルーチンワークは問題なし。午後は集中力を保ち、ダブルチェックを忘れずに。今日の慎重さが、明日の安心につながります。",
                "チームワークが鍵となる日。同僚とランチをしながら情報交換を。協力することで大きな成果が生まれます。あなたのサポートがプロジェクトを成功に導きます。夕方には達成感を味わえるはず。今日の絆が、未来の成功の種になります。",
                "クリエイティブなアイデアが湧く日。ノートにメモして上司に提案を。午後のブレストで活躍のチャンス。新しいツールを試すのも吉。今日のひらめきが、プロジェクトの転機になります。自信を持って発言を！",
                "デッドラインに追われやすい日。優先順位をリストアップして効率化。午前中は集中作業、午後はレビューを。ミスを防ぐダブルチェックが鍵。今日の頑張りが、明日の余裕を生みます。",
                "ネットワークが広がる日。社外の人とのミーティングがチャンス。名刺交換を積極的に。夕方のメールフォローが重要。今日のつながりが、将来のビジネスチャンスを呼び込みます。",
                "ルーチンワークが中心の日。細かい作業に集中してクオリティを上げて。午後はスキルを磨く時間に。オンライン講座がおすすめ。今日の積み重ねが、キャリアアップの基盤になります。",
                "上司からのフィードバックが得られる日。積極的に相談を。午後の話し合いが成長のきっかけに。アドバイスをメモして実践。今日の学びが、明日の自信につながります。"
            ],
            money: [
                "臨時収入の可能性大！宝くじやクーポン当選も。財布の中を整理して運気をアップ。夕方以降に良い知らせが届くかも。無駄遣いは控え、貯蓄を意識して。今日の小さな節約が、来月の大きな余裕を生みます。金運は好調！",
                "金運は安定傾向。無駄遣いを控えて貯蓄を。コンビニよりスーパーで買い物がお得。節約が未来の豊かさにつながります。今日の買い物はリストを作ってから。計画的な出費が、安心感を生みます。",
                "投資や副業に良いタイミング。興味のあるセミナーに参加してみて。情報収集が将来の資産を増やす第一歩に。今日の学びが、来月の収入源になるかも。焦らず、じっくり検討を。",
                "出費が少し増えそう。予定外の飲み会に注意。予算を決めておくと安心です。翌日は節約モードでリカバリーを。今日の我慢が、来週の余裕を生みます。計画性が金運の鍵。",
                "金運アップの兆し！財布を新調するのも吉。ピンクやゴールドの小物がラッキー。整理整頓で運気もクリアに！今日の行動が、来月の臨時収入を引き寄せます。",
                "副収入のチャンスあり。フリマアプリで不用品を売ってみて。夕方に良いオファーが来るかも。貯金口座をチェックして計画を。今日の工夫が、来年の資産増加につながります。",
                "ショッピング運が良い日。必要なものを安く手に入れるチャンス。クーポンアプリを活用して。無駄遣いは避け、予算内を守って。今日の賢い選択が、金運を安定させます。",
                "金銭トラブルに注意。契約書は細かく確認を。午後は銀行手続きが吉。専門家に相談するのもおすすめ。今日の慎重さが、将来の損失を防ぎます。",
                "貯蓄運が高まる日。定期預金を検討して。小さな積み立てが効果大。夕方の投資情報チェックがヒントに。今日のスタートが、豊かな未来を築きます。",
                "意外な返金やボーナスが入るかも。領収書を整理してチャンスを待って。無駄なサブスクを解約。今日の整理が、金運の流れを良くします。"
            ],
            health: [
                "体調は絶好調！朝からエネルギーが満ちています。軽いジョギングやヨガでさらに活性化。夜はぐっすり眠れて明日も元気！水分補給とストレッチを忘れずに。今日の健康管理が、来週の活力になります。",
                "疲れが溜まりやすい日。昼休みに5分仮眠を。カフェインよりハーブティーがおすすめ。夜10時までに就寝を心がけて。今日の休息が、明日のパフォーマンスを上げます。",
                "運動に最適な日！公園で散歩が気分転換に。ストレッチで肩こりも解消。体を動かすと運気もアップします。夕方の軽い運動が、夜の良質な睡眠を約束します。",
                "睡眠不足に注意。昨夜の寝不足が響きそう。昼間に15分のパワーナップを。夜はスマホを早めにオフにしてリラックス。今日の睡眠が、明日の集中力を支えます。",
                "体調管理はバッチリ！バランスの良い食事を。野菜中心のランチで内側からキレイに。水分補給も忘れずに！今日の習慣が、来月の健康診断で良い結果に。",
                "免疫力アップの日。ビタミン豊富なフルーツを摂取して。軽い散歩で新鮮な空気を。夜はアロマでリラックス。今日のケアが、風邪知らずの体を作ります。",
                "ストレス溜まりやすい日。深呼吸や瞑想を試して。午後のティータイムがおすすめ。早めの就寝で回復を。今日のメンタルケアが、明日の活力を生みます。",
                "ダイエット運が良い日。ヘルシーな食事メニューを。ジョギングやウォーキングでカロリー消費。水分を多めに摂ってデトックス。今日の努力が、体型維持に繋がります。",
                "眼精疲労に注意。PC作業中は休憩を挟んで。ブルーライトカットメガネが吉。夜は温かい目元パックでリラックス。今日の予防が、明日のクリアな視界を保ちます。",
                "全体的に健康運高め。ヨガやピラティスで体を整えて。栄養バランスの取れた夕食を。今日のルーチンが、長期的なウェルネスをサポートします。"
            ],
            lucky: [
                "ラッキーカラー：ピンク / ラッキーアイテム：花。カフェの窓際席が吉。花を飾ると運気アップ！ピンクの小物を身につけて出かけると、良い出会いが。",
                "ラッキーカラー：ブルー / ラッキーアイテム：本。図書館や本屋が開運スポット。青いペンでメモを。読書が運を引き寄せます。",
                "ラッキーカラー：グリーン / ラッキーアイテム：植物。公園や観葉植物のある場所が吉。緑の服も幸運を呼ぶ。",
                "ラッキーカラー：ゴールド / ラッキーアイテム：鏡。鏡の前で笑顔の練習を。金色のアクセが運を呼ぶ。",
                "ラッキーカラー：ホワイト / ラッキーアイテム：手帳。スケジュール管理が運を整える。白い文具がラッキー。",
                "ラッキーカラー：レッド / ラッキーアイテム：キャンドル。赤い服でエネルギーチャージ。夜のキャンドルライトが幸運を照らします。",
                "ラッキーカラー：パープル / ラッキーアイテム：宝石。神秘的な場所が吉。紫のアクセで直感力がアップします。",
                "ラッキーカラー：イエロー / ラッキーアイテム：太陽モチーフ。明るい場所で過ごして。黄色の小物がポジティブエネルギーを呼び込みます。",
                "ラッキーカラー：ブラック / ラッキーアイテム：鍵。黒いバッグで守護運アップ。鍵状のペンダントがチャンスの扉を開きます。",
                "ラッキーカラー：オレンジ / ラッキーアイテム：果物。活発な行動が吉。オレンジ色の服で元気をチャージします。"
            ],
            quotes: [
                "「運は勇気ある者に味方する。今日の一歩が、明日の奇跡を呼ぶ。あなたには無限の可能性がある。信じて進んでください。」",
                "「小さな親切が大きな幸せを連れてくる。笑顔を忘れないで。今日の優しさが、明日あなたに返ってきます。」",
                "「失敗は成功への近道。今日のつまずきは、明日の飛躍のバネになる。諦めない心が運命を変える。」",
                "「自分を信じることが、運命を変える鍵。あなたはもっと輝ける。今日がそのスタートラインです。」",
                "「感謝の心が幸運の扉を開く。『ありがとう』を3回言ってみて。今日の感謝が、明日の幸せを呼び込む。」",
                "「人生は鏡。あなたが出すエネルギーが返ってくる。今日のポジティブが、明日の幸運を形作る。」",
                "「挑戦なくして成長なし。今日の小さなリスクが、大きな報酬を生む。恐れず前進を。」",
                "「運命は自分で描くもの。今日の選択が、未来の道を照らす。賢く選んでください。」",
                "「静かな心が幸運を呼ぶ。今日の瞑想が、明日のインスピレーションを生む。」",
                "「友情が運の源。今日のつながりが、明日のチャンスを広げる。感謝を忘れずに。」"
            ]
        };

        // 正しい星座判定
        function getZodiac(m, d) {
            if ((m == 1 && d >= 20) || (m == 2 && d <= 18)) return "水瓶座";
            if ((m == 2 && d >= 19) || (m == 3 && d <= 20)) return "魚座";
            if ((m == 3 && d >= 21) || (m == 4 && d <= 19)) return "牡羊座";
            if ((m == 4 && d >= 20) || (m == 5 && d <= 20)) return "牡牛座";
            if ((m == 5 && d >= 21) || (m == 6 && d <= 21)) return "双子座";
            if ((m == 6 && d >= 22) || (m == 7 && d <= 22)) return "蟹座";
            if ((m == 7 && d >= 23) || (m == 8 && d <= 22)) return "獅子座";
            if ((m == 8 && d >= 23) || (m == 9 && d <= 22)) return "乙女座";
            if ((m == 9 && d >= 23) || (m == 10 && d <= 23)) return "天秤座";
            if ((m == 10 && d >= 24) || (m == 11 && d <= 22)) return "蠍座";
            if ((m == 11 && d >= 23) || (m == 12 && d <= 21)) return "射手座";
            if ((m == 12 && d >= 22) || (m == 1 && d <= 19)) return "山羊座";
            return "不明";
        }

        // 星評価（各カテゴリ独立でバラバラに計算、全体運なし）
        function getStars(seed) {
            return Math.floor(Math.abs(seed * 31) % 100 / 20) + 1;
        }

        window.addEventListener('load', () => {
            const b = localStorage.getItem('userBirthday');
            const t = localStorage.getItem('userBloodtype');
            if (b) document.getElementById('birthday').value = b;
            if (t) document.getElementById('bloodtype').value = t;
        });

        function calculateFortune() {
            const birthday = document.getElementById('birthday').value;
            const bloodtype = document.getElementById('bloodtype').value;
            if (!birthday || !bloodtype) return alert('生年月日と血液型を入力してください！');

            localStorage.setItem('userBirthday', birthday);
            localStorage.setItem('userBloodtype', bloodtype);

            const [y, m, d] = birthday.split('-').map(Number);
            const zodiac = getZodiac(m, d);

            const today = new Date();
            const tomorrow = new Date(today); tomorrow.setDate(tomorrow.getDate() + 1);

            // シードを日付・生年月日・血液型で変化、各カテゴリ独立
            const todaySeed = y * 10000 + m * 100 + d + today.getFullYear() * 1000000 + (today.getMonth() + 1) * 10000 + today.getDate() + bloodtype.charCodeAt(0) * 100;
            const tomorrowSeed = y * 10000 + m * 100 + d + tomorrow.getFullYear() * 1000000 + (tomorrow.getMonth() + 1) * 10000 + tomorrow.getDate() + bloodtype.charCodeAt(0) * 100;

            displayResult('todayResult', '今日の運勢', todaySeed, zodiac, bloodtype);
            displayResult('tomorrowResult', '明日の運勢', tomorrowSeed, zodiac, bloodtype);
        }

        function displayResult(id, title, seed, zodiac, bloodtype) {
            const c = document.getElementById(id);
            let html = `<div class="day-title">${title}</div><p style="text-align:center;"><strong>${zodiac}・${bloodtype}型</strong></p>`;
            const cats = ['love','work','money','health','lucky'];
            const labels = ['恋愛','仕事','金運','健康','ラッキー'];

            cats.forEach((cat, i) => {
                // 各カテゴリで独自のシードを使ってバラバラな星数とメッセージ選択
                const catSeed = seed + i * 12345 + Math.floor(Math.random() * 100); // 追加のランダム要素でバラバラ強化
                const idx = Math.abs(catSeed % fortuneDB[cat].length);
                const stars = getStars(catSeed);
                const starHtml = '★'.repeat(stars) + '☆'.repeat(5 - stars);
                const text = fortuneDB[cat][idx];
                html += `<div class="fortune-item">
                    <span class="fortune-label">${labels[i]}：</span><span class="stars">${starHtml}</span><br>
                    <span style="margin-top:8px;display:block;">${text}</span>
                </div>`;
            });

            const qIdx = Math.abs(seed % fortuneDB.quotes.length);
            html += `<div class="quote"><strong>占い師からのメッセージ：</strong><br>${fortuneDB.quotes[qIdx]}</div>`;

            c.innerHTML = html;
            c.style.display = 'block';
        }

        function resetData() {
            if (confirm('本当にリセットしますか？\n入力データがすべて消去されます。')) {
                localStorage.clear();
                document.getElementById('birthday').value = '';
                document.getElementById('bloodtype').value = '';
                document.getElementById('todayResult').style.display = 'none';
                document.getElementById('tomorrowResult').style.display = 'none';
            }
        }
    </script>
</body>
</html>
