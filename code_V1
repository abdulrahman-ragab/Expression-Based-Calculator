#include <iostream>
#include <string>
#include <vector>
#include <cctype>
#include <stdexcept>

// ==== Token Types ====
enum class TokenType {
    Number, Plus, Minus, Mul, Div, LParen, RParen, End
};

struct Token {
    TokenType type;
    double value = 0;

    Token(TokenType t, double v = 0) : type(t), value(v) {}
};

// ==== Lexer ====
class Lexer {
    std::string text;
    size_t pos = 0;

public:
    explicit Lexer(const std::string& input) : text(input) {}

    Token getNextToken() {
        while (pos < text.size() && isspace(text[pos])) ++pos;

        if (pos >= text.size()) return Token(TokenType::End);

        char current = text[pos];

        if (isdigit(current) || current == '.') {
            double number = 0;
            size_t start = pos;
            while (pos < text.size() && (isdigit(text[pos]) || text[pos] == '.')) ++pos;
            number = std::stod(text.substr(start, pos - start));
            return Token(TokenType::Number, number);
        }

        ++pos;
        switch (current) {
            case '+': return Token(TokenType::Plus);
            case '-': return Token(TokenType::Minus);
            case '*': return Token(TokenType::Mul);
            case '/': return Token(TokenType::Div);
            case '(': return Token(TokenType::LParen);
            case ')': return Token(TokenType::RParen);
            default:
                throw std::runtime_error(std::string("Unknown character: ") + current);
        }
    }
};

// ==== Parser ====
class Parser {
    Lexer lexer;
    Token current;

    void eat(TokenType type) {
        if (current.type == type)
            current = lexer.getNextToken();
        else
            throw std::runtime_error("Unexpected token");
    }

    double factor() {
        if (current.type == TokenType::Number) {
            double val = current.value;
            eat(TokenType::Number);
            return val;
        } else if (current.type == TokenType::LParen) {
            eat(TokenType::LParen);
            double result = expr();
            eat(TokenType::RParen);
            return result;
        } else if (current.type == TokenType::Minus) {
            eat(TokenType::Minus);
            return -factor();
        }
        throw std::runtime_error("Invalid factor");
    }

    double term() {
        double result = factor();
        while (current.type == TokenType::Mul || current.type == TokenType::Div) {
            if (current.type == TokenType::Mul) {
                eat(TokenType::Mul);
                result *= factor();
            } else {
                eat(TokenType::Div);
                double divisor = factor();
                if (divisor == 0) throw std::runtime_error("Division by zero");
                result /= divisor;
            }
        }
        return result;
    }

    double expr() {
        double result = term();
        while (current.type == TokenType::Plus || current.type == TokenType::Minus) {
            if (current.type == TokenType::Plus) {
                eat(TokenType::Plus);
                result += term();
            } else {
                eat(TokenType::Minus);
                result -= term();
            }
        }
        return result;
    }

public:
    explicit Parser(Lexer l) : lexer(std::move(l)), current(lexer.getNextToken()) {}

    double parse() {
        return expr();
    }
};

// ==== Main ====
int main() {
    std::cout << "Simple Calculator (type 'exit' to quit)\n";
    std::string input;

    while (true) {
        std::cout << ">>> ";
        std::getline(std::cin, input);
        if (input == "exit") break;

        try {
            Lexer lexer(input);
            Parser parser(lexer);
            double result = parser.parse();
            std::cout << "Result: " << result << "\n";
        } catch (const std::exception& e) {
            std::cerr << "Error: " << e.what() << "\n";
        }
    }

    return 0;
}
