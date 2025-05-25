---
title: "AI Agent は，TypeScriptの型安全性を活用して開発しよう！"
emoji: "🔥"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: [Genkit, TypeScript,contest2025ts]
published: true
---

# はじめに

今年は，AIAgentアプリを開発するハッカソンが多く開催されていて，初心者歓迎もあって嬉しいですね．他の参加者との実力差を実感する良い経験になるでしょう。

ただ，LLMの性能をアプリケーションに組み込むには，データ構造の定義が重要になってきます。

そこで今回は，AIオーケストレーション用のサーバーサイドにGenkitを採用し，TypeScriptで開発する際にZodスキーマを活用した事例を紹介します。

# 対象者

- 初心者歓迎ハッカソンに参加する勇敢な初心者
- TypeScriptやAIエージェント開発に興味がある方

# Zodとは？

Zodは，TypeScriptファーストのスキーマ宣言およびバリデーションライブラリです。

主な特徴は以下の通りです。

1. TypeScriptの型安全性の恩恵を享受できる
2. 多様なデータ構造を直感的にスキーマ定義できる
3. データのバリデーションが詳細に行える
4. スキーマを組み合わせたり，データを適切な型に変換することが容易

Zodを使うことで，データ形状が適切であることを保証でき，AIAgentの安定性や信頼性を高められます。

# 具体例

この記事では、AIオーケストレーション用のサーバーサイドにGenkitを採用しています。
また、DotPromptを用いて、以下のようなモデル設定例を使っています。

[Genkit公式ドキュメント](https://genkit.dev)
[DotPromptの解説](https://genkit.dev/docs/dotprompt/)

```yaml:helloGemini.prompt
---
model: vertexai/gemini-2.0-flash-lite
config:
  temperature: 1.4
  topK: 50
  topP: 0.4
  maxOutputTokens: 400
  stopSequences:
    -   "<end>"
    -   "<fin>"
---
You are the world's most welcoming AI assistant. Greet the user and offer your assistance.
```

以下は、Zodを使ってAIエージェントの入力・出力スキーマを定義し、型安全にフローを実装する例です。

```ts:helloGeminiflow.ts
import { z, genkit } from 'genkit';
import { vertexAI } from '@genkit-ai/vertexai';

export const ai = genkit({
  plugins: [vertexAI()],
});

export const helloGemini = ai.defineFlow(
    {
        name: `hello-Gemini`,
        inputSchema: z.object({
            text: z.string(),
        }),
        outputSchema: z.object({
            text: z.string(),
        }),
    },
    async (input) => {
        const helloGeminiPrompt = ai.prompt("helloGemini");
        const response = await helloGeminiPrompt(input.text);
        const output = {text: response.text };
        return output;
    }
)
```

この例では、入力と出力のスキーマをZodで定義することで、TypeScriptの型推論や型安全性を活かしつつ、データのバリデーションも自動的に行えます。

# まとめ

Zodスキーマで定義することで，TypeScriptの型安全性を享受でき，AIAgentの信頼性（型エラーの早期発見やバリデーションの自動化）と保守性を向上させることができます。

エンジニアだけでなく，AIAgentに多くの実装を行う場合，データ構造を直感的に定義でき，アプリをより良いものにできるという点で初心者にとっても有用です。私自身もAIAgent開発で活用しています!!
