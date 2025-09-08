import React, { useState, useRef, useEffect } from 'react';
import { Send, Sparkles, MessageSquare, Languages, BookOpen, PenTool, Mic2, Volume2 } from 'lucide-react';

const ArabicAIAssistant = () => {
  const [messages, setMessages] = useState([]);
  const [inputText, setInputText] = useState('');
  const [isLoading, setIsLoading] = useState(false);
  const messagesEndRef = useRef(null);

  const scrollToBottom = () => {
    messagesEndRef.current?.scrollIntoView({ behavior: "smooth" });
  };

  useEffect(() => {
    scrollToBottom();
  }, [messages]);

  // Détection de la langue arabe
  const detectArabic = (text) => {
    const arabicRegex = /[\u0600-\u06FF]/;
    return arabicRegex.test(text);
  };

  // Suggestions de questions/tâches
  const suggestions = [
    {
      category: "Traduction",
      icon: Languages,
      items: [
        "Traduire ce texte en arabe",
        "ترجم هذا النص إلى الفرنسية",
        "Comment dit-on 'bonjour' en arabe ?",
        "ما معنى هذه الكلمة بالفرنسية؟"
      ]
    },
    {
      category: "Grammaire",
      icon: BookOpen,
      items: [
        "Expliquer les règles du féminin en arabe",
        "اشرح لي قواعد الجمع في العربية",
        "Conjuguer le verbe كتب",
        "Analyser cette phrase grammaticalement"
      ]
    },
    {
      category: "Écriture",
      icon: PenTool,
      items: [
        "Corriger mon texte arabe",
        "اكتب لي رسالة رسمية",
        "Aider à rédiger un email en arabe",
        "تدقيق إملائي ونحوي"
      ]
    },
    {
      category: "Conversation",
      icon: MessageSquare,
      items: [
        "Apprendre des expressions arabes courantes",
        "علمني كلمات فرنسية جديدة",
        "Dialogue simple en arabe",
        "محادثة باللغة العربية"
      ]
    }
  ];

  // Simulation de réponse IA
  const generateResponse = (userInput) => {
    const responses = {
      translate: {
        ar: "سأساعدك في الترجمة. من فضلك أرسل النص الذي تريد ترجمته وحدد اللغة المطلوبة.",
        fr: "Je vais vous aider avec la traduction. Envoyez-moi le texte à traduire et spécifiez la langue souhaitée."
      },
      grammar: {
        ar: "ممتاز! سأشرح لك قواعد اللغة العربية بطريقة مبسطة. ما هو موضوعك النحوي المحدد؟",
        fr: "Parfait ! Je vais vous expliquer la grammaire arabe de manière simple. Quel est votre sujet grammatical spécifique ?"
      },
      writing: {
        ar: "سأساعدك في تحسين كتابتك العربية. أرسل النص وسأقوم بتصحيحه وتحسينه.",
        fr: "Je vais vous aider à améliorer votre écriture arabe. Envoyez le texte et je le corrigerai et l'améliorerai."
      },
      conversation: {
        ar: "أهلاً وسهلاً! دعنا نتحدث باللغة العربية. ما الموضوع الذي يهمك؟",
        fr: "Bienvenue ! Parlons en arabe. Quel sujet vous intéresse ?"
      },
      default: {
        ar: "فهمت سؤالك. كيف يمكنني مساعدتك بالتحديد؟",
        fr: "J'ai compris votre question. Comment puis-je vous aider précisément ?"
      }
    };

    // Logique simple de détection d'intention
    const input = userInput.toLowerCase();
    
    if (input.includes('tradui') || input.includes('ترجم') || input.includes('translate')) {
      return responses.translate;
    } else if (input.includes('grammar') || input.includes('نحو') || input.includes('قواعد')) {
      return responses.grammar;
    } else if (input.includes('écri') || input.includes('كتاب') || input.includes('تصحيح')) {
      return responses.writing;
    } else if (input.includes('conversation') || input.includes('محادثة') || input.includes('dialogue')) {
      return responses.conversation;
    }
    
    return responses.default;
  };

  const handleSendMessage = async () => {
    if (!inputText.trim()) return;

    const isArabic = detectArabic(inputText);
    
    // Ajouter le message utilisateur
    const userMessage = {
      id: Date.now(),
      type: 'user',
      content: inputText,
      isArabic: isArabic,
      timestamp: new Date()
    };

    setMessages(prev => [...prev, userMessage]);
    setInputText('');
    setIsLoading(true);

    // Simuler une réponse avec délai
    setTimeout(() => {
      const response = generateResponse(inputText);
      const assistantMessage = {
        id: Date.now() + 1,
        type: 'assistant',
        content: response.ar,
        contentFr: response.fr,
        timestamp: new Date()
      };
      
      setMessages(prev => [...prev, assistantMessage]);
      setIsLoading(false);
    }, 1500);
  };

  const handleSuggestionClick = (suggestion) => {
    setInputText(suggestion);
  };

  const handleKeyPress = (e) => {
    if (e.key === 'Enter' && !e.shiftKey) {
      e.preventDefault();
      handleSendMessage();
    }
  };

  return (
    <div className="min-h-screen bg-gradient-to-br from-yellow-50 via-amber-50 to-yellow-100">
      {/* Header */}
      <div className="bg-black/90 backdrop-blur-sm border-b border-yellow-400 shadow-lg sticky top-0 z-10">
        <div className="max-w-4xl mx-auto px-4 py-4">
          <div className="flex items-center gap-3">
            <div className="p-2 bg-yellow-400 rounded-lg">
              <Sparkles className="h-6 w-6 text-black" />
            </div>
            <div>
              <h1 className="text-xl font-bold text-yellow-400">مساعد اللغة العربية</h1>
              <p className="text-sm text-yellow-200">Assistant IA pour l'apprentissage de l'arabe</p>
            </div>
          </div>
        </div>
      </div>

      <div className="max-w-4xl mx-auto px-4 py-6">
        {/* Messages ou Suggestions */}
        {messages.length === 0 ? (
          <div className="space-y-6">
            <div className="text-center py-8">
              <div className="inline-flex p-4 bg-yellow-400 rounded-full mb-4">
                <MessageSquare className="h-8 w-8 text-black" />
              </div>
              <h2 className="text-2xl font-bold text-black mb-2">
                مرحباً! كيف يمكنني مساعدتك؟
              </h2>
              <p className="text-gray-700 mb-8">
                Choisissez une suggestion ou posez votre propre question
              </p>
            </div>

            {/* Suggestions */}
            <div className="grid gap-6 md:grid-cols-2">
              {suggestions.map((category) => (
                <div key={category.category} className="bg-black rounded-xl shadow-lg border border-yellow-400 p-6">
                  <div className="flex items-center gap-3 mb-4">
                    <category.icon className="h-5 w-5 text-yellow-400" />
                    <h3 className="font-semibold text-yellow-400">{category.category}</h3>
                  </div>
                  <div className="space-y-2">
                    {category.items.map((item, index) => (
                      <button
                        key={index}
                        onClick={() => handleSuggestionClick(item)}
                        className="w-full text-left p-3 rounded-lg hover:bg-yellow-400 hover:text-black transition-colors border border-transparent hover:border-yellow-400 text-sm text-white"
                        dir={detectArabic(item) ? 'rtl' : 'ltr'}
                      >
                        {item}
                      </button>
                    ))}
                  </div>
                </div>
              ))}
            </div>
          </div>
        ) : (
          /* Zone de messages */
          <div className="space-y-4 mb-6">
            {messages.map((message) => (
              <div
                key={message.id}
                className={`flex ${message.type === 'user' ? 'justify-end' : 'justify-start'}`}
              >
                <div
                  className={`max-w-2xl px-4 py-3 rounded-2xl ${
                    message.type === 'user'
                      ? 'bg-yellow-400 text-black font-medium'
                      : 'bg-black text-yellow-400 border border-yellow-400 shadow-lg'
                  }`}
                  dir={message.type === 'assistant' || message.isArabic ? 'rtl' : 'ltr'}
                >
                  <p className="text-sm leading-relaxed">{message.content}</p>
                  {message.type === 'assistant' && message.contentFr && (
                    <div className="mt-3 pt-3 border-t border-yellow-600">
                      <p className="text-xs text-yellow-200" dir="ltr">
                        <strong>Traduction :</strong> {message.contentFr}
                      </p>
                    </div>
                  )}
                  <p className="text-xs opacity-60 mt-2">
                    {message.timestamp.toLocaleTimeString()}
                  </p>
                </div>
              </div>
            ))}
            
            {isLoading && (
              <div className="flex justify-start">
                <div className="bg-black border border-yellow-400 shadow-lg rounded-2xl px-4 py-3">
                  <div className="flex items-center gap-2">
                    <div className="flex gap-1">
                      <div className="w-2 h-2 bg-yellow-400 rounded-full animate-bounce"></div>
                      <div className="w-2 h-2 bg-yellow-400 rounded-full animate-bounce" style={{animationDelay: '0.1s'}}></div>
                      <div className="w-2 h-2 bg-yellow-400 rounded-full animate-bounce" style={{animationDelay: '0.2s'}}></div>
                    </div>
                    <span className="text-sm text-yellow-400">جاري الكتابة...</span>
                  </div>
                </div>
              </div>
            )}
            <div ref={messagesEndRef} />
          </div>
        )}

        {/* Zone de saisie */}
        <div className="bg-black rounded-2xl shadow-lg border border-yellow-400 p-4">
          <div className="flex gap-3">
            <div className="flex-1">
              <textarea
                value={inputText}
                onChange={(e) => setInputText(e.target.value)}
                onKeyPress={handleKeyPress}
                placeholder={detectArabic(inputText) ? "اكتب سؤالك أو طلبك هنا..." : "Posez votre question ou décrivez votre besoin..."}
                className="w-full p-3 border-0 resize-none focus:outline-none text-sm bg-transparent text-yellow-400 placeholder-yellow-600"
                rows="3"
                dir={detectArabic(inputText) ? 'rtl' : 'ltr'}
              />
            </div>
            
            <div className="flex flex-col gap-2">
              <button
                onClick={handleSendMessage}
                disabled={!inputText.trim() || isLoading}
                className="p-3 bg-yellow-400 text-black rounded-xl hover:bg-yellow-300 transition-colors disabled:opacity-50 disabled:cursor-not-allowed font-medium"
              >
                <Send size={20} />
              </button>
              
              <button className="p-3 bg-gray-800 text-yellow-400 rounded-xl hover:bg-gray-700 transition-colors border border-yellow-600">
                <Mic2 size={20} />
              </button>
            </div>
          </div>
          
          <div className="flex justify-between items-center mt-3 text-xs text-yellow-300">
            <span>Entrée pour envoyer • Shift+Entrée pour nouvelle ligne</span>
            <span className="flex items-center gap-4">
              <span className="px-2 py-1 bg-yellow-400 text-black rounded-full text-xs font-medium">
                {detectArabic(inputText) ? 'عربي' : 'Français'}
              </span>
              <span>{inputText.length} caractères</span>
            </span>
          </div>
        </div>
      </div>
    </div>
  );
};

export default ArabicAIAssistant;
