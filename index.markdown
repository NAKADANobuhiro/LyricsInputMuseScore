---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---
<script>
    //lyrics に入力されたテキストの漢字をGooラボのひらがな化API ひらがなに変換して、lyrics_kana に入れる
    function convertKana() {
        let lyrics = document.getElementById("lyrics").value;
        let lyrics_kana = document.getElementById("lyrics_kana");

        //lyrics から句読点"、。"を除去する。
        lyrics = lyrics.replace(/[、。]/g, "");

        let url = "https://labs.goo.ne.jp/api/hiragana";
        let data = {
            "app_id": "5cf24bdf1cd2fe5e261f5ccbaa363a2158e54ca212e1d742306e635f58ea530f",
            "sentence": lyrics,
            "output_type": "hiragana"
        };
        let xhr = new XMLHttpRequest();
        xhr.open("POST", url, true);
        xhr.setRequestHeader("Content-Type", "application/json");
        xhr.onreadystatechange = function() {
            if (xhr.readyState === 4 && xhr.status === 200) {
                let response = JSON.parse(xhr.responseText);
                lyrics_kana.value = response.converted;
            }
        };
        xhr.send(JSON.stringify(data));
    }

    function putSpace() {
        let lyrics_kana = document.getElementById("lyrics_kana").value;
        //lyrics_kana に連続した空白があれば、一つにする
        lyrics_kana = lyrics_kana.replace(/\s+/g, " ");
        let lyrics_space = document.getElementById("lyrics_space");
        lyrics_space.value = putSpacesBetweenLetters(lyrics_kana);
    }

// lyrics_kana の一文字ごとに空白を入れて、lyrics_space に格納する関数 putSpacesBetweenLetters を作成する ただし、次の文字の前には空白を入れない "んっゃゅょぁぃぅぇぉー" ただし、既に空白が入っている場合は、空白を入れない

function putSpacesBetweenLetters(lyrics_kana) {

    let lyrics_space = '';
    const noSpaceBefore = "っゃゅょぁぃぅぇぉー";

    for (let i = 0; i < lyrics_kana.length; i++) {
        if (lyrics_kana[i] !== ' ' && !(i < lyrics_kana.length - 1 && noSpaceBefore.includes(lyrics_kana[i + 1]))) {
            lyrics_space += lyrics_kana[i] + ' ';
        } else {
            lyrics_space += lyrics_kana[i];
        }
    }

    // lyrics_space に連続した空白があれば、一つにする
    lyrics_space = lyrics_space.replace(/\s+/g, " ");

    //行末に空白があれば、削除する
    lyrics_space = lyrics_space.replace(/\s+$/g, "");

    return lyrics_space;
}

// lyrics_space の値をクリップボードにコピーする関数 copytoClipboard() を作成する
function copytoClipboard() {
    let lyrics_space = document.getElementById("lyrics_space").value;
    navigator.clipboard.writeText(lyrics_space);
}

</script>

## 使い方

1. 漢字を含む歌詞を入力して、[ひらがなに変換]を押してください。
2. ひらがなに変換された歌詞が表示されます。[空白を挿入]を押してください。
3. 空白の入った歌詞が表示されます。[クリップボードにコピー]を押してください。
4. MuseScore で最初の音符を選択して[Crtl]+[L]で歌詞入力になったら、[Ctrl]+[V]を繰り返し押して貼り付けてください。

<label>1. 漢字を含む歌詞</label>
<textarea id="lyrics" rows="5" cols="80" value="">シャボン玉飛んだ、屋根まで飛んだ、
屋根まで飛んで、こわれて消えた、
風風吹くな、シャボン玉飛ばそ
</textarea>
<input type="button" value="ひらがなに変換" onclick="convertKana()" />

<label>2. ひらがなの歌詞</label>
<textarea id="lyrics_kana" rows="5" cols="80" value=""></textarea>
<input type="button" value="空白を挿入" onclick="putSpace()" />

<label>3. 空白の入った歌詞</label>
<textarea id="lyrics_space" rows="5" cols="80" value=""></textarea>
<input type="button" value="クリップボードにコピー" onclick="copytoClipboard()" />

## 注意制約事項

- 自分用ツールのため、手抜き仕様ですがご了承ください。
- 漢字ひらがな変換は、[Gooラボのひらがな化API](https://labs.goo.ne.jp/api/jp/hiragana-translation/)を使用しています。
- カタカナはひらがなに変換せずそのままにしたかったのですが、API の仕様上ひらがなに変換されてしまいます。
- "ん" に、音符を割り当てないケースもあると思いますが、それは適宜修正してください。
- 例えば、"明日"は、"あした"と"あす"の読み方があるように、変換できないケースは適宜修正してください。"シャボン玉"でも、"**風風**吹くな"は、"**かぜかぜ**ふくな"ではなく、"**かぜふう**ふくな"となってしまいます。

## 作成の動機

- 私は音楽製作に FL Studio を使用していますが、まれに楽譜を印刷してと要望されることがあります。
- しかし、FL Studio は楽譜の印刷はおろか五線譜の表示もできません。(音楽製作は Piano Roll で十分です。)
- そこで FL Studio から、MIDI 形式で出力し、MuseScore 4 で楽譜を作成しました。
- MuseScore での楽譜の作成は簡単ですが、歌詞の入力は大変です。音符をクリックして、[Crtl]+[L] で歌詞を一音分入力して、スペースを押して、次の一音分の歌詞を入力して、という作業をひたすら繰り返すのは大変です。
- いろいろ調べてみたところ、MuseScore で歌詞を入力するには、歌詞の一音分ごとにスペースを入れて、貼り付けるという方法があることがわかりました。
- これがわかれば、歌詞を入力するのは簡単です。その前処理として、漢字をひらがなに変換し、一文字ごとにスペースを入れ、クリップボードにコピーするツールとして本サイトを作成しました。
- また、GitHub Copilot のサブスクリプションに入ったので、試してみたくて本サイトを作成しました。コードもさることながら、文章の生成もできるようになっていて驚きました。

2024-01-05(金) 作成