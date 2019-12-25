import re

def read_data(fileName):
    """

    :param fileName: name of file
    :return: cleaned list of strings
    :except: return error if code fails
    """
    try:
        f = open(fileName, "r")
        list_1 = f.readlines()
        f.flush()
        f.close()
        return list_1

    except:
        print 'Error'


def clean_data(my_data):
    """
    :param my_data: list of lines with uncleaned data
    :return: returns a list of lines which have been cleaned
    :except: return error if code fails

    """
    try:
        cleaned = []
        for data in my_data:
            i = 0
            data = data.lower()
            data = " " + data + " "
            data = re.sub(r"([a-z0-9]+)([-][-]+)([0-9a-z]+)", r"\1 \3", data)
            data = re.sub("[a-z]+[-][a-z]+", "", data)
            data = re.sub('[^a-zA-Z0-9 \']', '', data)
            data = re.sub(r"( (\d[.])?\d+)([a-z]+)", r'\1 \3', data)
            data = re.sub(r"([a-z]+)((\d[.])?\d+ )", r'\1 \2', data)

            data = re.sub('([0-9]*[.])?[0-9]+ ', 'num ', data)
            data = re.sub("(num )+", "num ", data)
            data = re.sub('[" "]+', " ", data)
            data = data.lstrip()
            data = data.rstrip()
            cleaned.append(data)

        return cleaned
    except:
        print 'Error'
        exit()


def build_n_gram_dict(n, cleaned_data):
    """
    :param n: the n in n-gram
    :param cleaned_data: list of cleaned sentences
    :return: dictionary of frequency of words
    :except: return error if code fails

    """
    # Catching incorrect data type

    dictionary = dict()
    for data in cleaned_data:
        data = "<s> "+data
        tokens = data.split(' ')
        for i in range(0, len(tokens)-n+1):
            word = ""
            for j in range(0, n):
                word = word+tokens[i+j]+" "
            word = word.rstrip()
            if dictionary.__contains__(word):
                i = dictionary.get(word)
                i = i+1
                dictionary[word] = i
            else:
                dictionary[word] = 1

    return dictionary


def calculate_PP(test_sentences, ngram_models):
    """
    :param test_sentences: list of sentences
    :param ngram_models: dictionary of dictionaries
    :return: average pp
    :except: return error if code fails

    """
    try:
        n = max(ngram_models.keys())
        v = len(ngram_models[1].keys())
        average =[]
        p = 1

        # Special case for n =1
        if n == 1:
            count = sum(ngram_models[1].values())+v
            for sentence in test_sentences:
                p = 1
                sentence = "<s> "+sentence
                t = sentence.split(" ")
                for w in t:
                    numerator = ngram_models[1].get(w, 0)+1
                    denominator = count
                    p = p*float(numerator)/denominator

                power = 1.0 / (len(sentence.split(" ")))
                per = pow(p, -power)
                average.append(per)

            return sum(average)/len(test_sentences)

        # General case when n not equal to 1
        for sentence in test_sentences:
            m = len(sentence.split(" "))
            sentence = "<s> "+sentence
            l = []
            tokens = sentence.split(" ")
            m = min(n, len(tokens))
            for i in range(1, m):
                word = ""
                for j in range(0, i+1):
                    word = word + tokens[j]+" "
                word = word.rstrip()
                l.append(word)

            for i in range(1, len(tokens)-n+1):
                word = ""
                for j in range(i, i+n):
                    word = word + tokens[j]+" "

                word = word.rstrip()

                l.append(word)

            ps = 1.0
            pp = []
            for s in l:
                    t = s.split(" ")
                    l = len(t)
                    word = ""
                    for i in range(0, l-1):
                        word = word + t[i]+" "
                    word = word.rstrip()
                    count_n = ngram_models[l].get(s, 0)+1
                    count_d = ngram_models[l-1].get(word, 0)+v
                    p = float(count_n)/count_d
                    ps = ps * p

            power = 1.0 / (len(sentence.split(" ")))
            per = pow(ps, -power)
            average.append(per)

        sum1 = 0
        for a in average:

            sum1 = sum1+a
        return float(sum1)/len(test_sentences)
    except:
        print 'Error'
        exit()


def generate_text(ngram_models, text_length, seed_word):
    """
    :param ngram_models: dictionary of dictionaries
    :param text_length: length of text to be generated
    :param seed_word: starting word
    :return: text of desired length
    :except: return error if code fails

    """
    try:

        n = max(ngram_models.keys())

        if text_length == 1:
            return "<s>"
        if text_length == 2:
            return "<s> "+seed_word

        v = len(ngram_models[1].keys())

        # putting all words in a list
        vocabulary_list = []
        for i in ngram_models[1].keys():
            vocabulary_list.append(i)

        if n == 1:
            maxp = 0
            for w in vocabulary_list:
                if ngram_models[1][w] > maxp:
                    maxp = ngram_models[1][w]
                    maxword = w
            text = "<s> "+seed_word+" "
            for i in range(2, text_length):
                text= text+maxword+" "

            return text.rstrip()

        length = text_length
        if n > 2:
            seed_word = "<s> "+seed_word
            length = length-2
        elif n == 2:
            seed_word = seed_word
            length=length-1

        text = seed_word+" "
        context = seed_word
        for i in range(0, length):
            prob = []
            for word in vocabulary_list:
                l = len(context.split(" "))
                count_denomenator = ngram_models[l].get(context, 0)
                count_denomenator = count_denomenator+v
                count_numerator = ngram_models[l+1].get(context+" "+word, 0)
                count_numerator = count_numerator+1
                p = float(count_numerator)/count_denomenator
                prob.append(p)
            text = text + vocabulary_list[prob.index(max(prob))]+" "
            context = context + " "+vocabulary_list[prob.index(max(prob))]
            l = len(text.split(" "))
            if l < n-1 or l == n-1:
                context = context

            elif l > n-1:
                t = context.split(" ")
                context = ""

                for k in range(len(t)-(n-1), len(t)):
                    context = context+t[k]+" "
                context = context.rstrip()

        if n == 2:
            last = text.split(" ")
            text = "<s> "
            for t in range(0, len(last)-2):
                text = text+last[t]+" "
            text = text.rstrip()

        return text
    except:
        print 'Error'
        exit()





