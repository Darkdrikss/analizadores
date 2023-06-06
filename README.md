import java.util.List;

public class SQLParser {
    private List<Token> tokens;
    private int currentTokenIndex;
    private Token currentToken;

    public SQLParser(List<Token> tokens) {
        this.tokens = tokens;
        this.currentTokenIndex = 0;
        this.currentToken = tokens.get(0);
    }

    public void parse() {
        selectStatement();
    }

    private void selectStatement() {
        match(TokenType.SELECT);

        // Verificar SELECT DISTINCT
        if (currentToken.getType() == TokenType.DISTINCT) {
            match(TokenType.DISTINCT);
        }

        selectList();

        match(TokenType.FROM);

        // Analizar múltiples tablas
        table();

        while (currentToken.getType() == TokenType.COMMA) {
            match(TokenType.COMMA);
            table();
        }

        // Clausula WHERE opcional
        if (currentToken.getType() == TokenType.WHERE) {
            match(TokenType.WHERE);
            condition();
        }

        // Clausula ORDER BY opcional
        if (currentToken.getType() == TokenType.ORDER) {
            match(TokenType.ORDER);
            match(TokenType.BY);
            orderByList();
        }

        match(TokenType.SEMICOLON);
    }

    private void selectList() {
        // Verificar si se seleccionan todas las columnas o columnas específicas
        if (currentToken.getType() == TokenType.ASTERISK) {
            match(TokenType.ASTERISK);
        } else {
            column();

            while (currentToken.getType() == TokenType.COMMA) {
                match(TokenType.COMMA);
                column();
            }
        }
    }

    private void table() {
        // Analizar alias de tabla
        if (currentToken.getType() == TokenType.IDENTIFIER) {
            match(TokenType.IDENTIFIER);

            // Verificar si hay un alias
            if (currentToken.getType() == TokenType.IDENTIFIER) {
                match(TokenType.IDENTIFIER);
            }
        } else {
            throw new SyntaxErrorException("Se esperaba un identificador de tabla");
        }
    }

    private void condition() {
        column();
        comparisonOperator();
        literal();
    }

    private void column() {
        // Verificar el formato tabla.columna
        if (currentToken.getType() == TokenType.IDENTIFIER) {
            match(TokenType.IDENTIFIER);

            // Verificar si hay una tabla antes del punto
            if (currentToken.getType() == TokenType.DOT) {
                match(TokenType.DOT);
                match(TokenType.IDENTIFIER);
            }
        } else {
            throw new SyntaxErrorException("Se esperaba un identificador de columna");
        }
    }

    private void comparisonOperator() {
        match(TokenType.EQUALS);
        // Alternativamente, puedes manejar aquí otros operadores de comparación
    }

    private void literal() {
        match(TokenType.STRING_LITERAL);
    }

    private void orderByList() {
        orderByExpression();

        while (currentToken.getType() == TokenType.COMMA) {
            match(TokenType.COMMA);
            orderByExpression();
        }
    }

    private void orderByExpression() {
        column();
        // Opcionalmente, maneja aquí la clasificación ASC/DESC
    }

    private void match(TokenType expectedType) {
        if (currentToken.getType() == expectedType) {
            consumeToken();
        } else {
            throw new SyntaxErrorException("Se esperaba " + expectedType + ", se encontró " + currentToken.getType());
        }
    }

    private void consumeToken() {
        currentTokenIndex++;
        if (currentTokenIndex < tokens.size()) {
            currentToken = tokens.get(currentTokenIndex);
   }
                                             
                                              
                                              //analizador sintáctico ascendente
    
                                              
 import java.util.List;

public class SQLParser {
    private List<Token> tokens;
    private int currentTokenIndex;
    private Token currentToken;

    public SQLParser(List<Token> tokens) {
        this.tokens = tokens;
        this.currentTokenIndex = 0;
        this.currentToken = tokens.get(0);
    }

    public void parse() {
        selectStatement();
        match(TokenType.EOF); // Verificar si se han analizado todos los tokens
    }

    // Producción: SELECT DISTINCT? selectList FROM table (',' table)* (WHERE condition)? (ORDER BY orderByList)? ';'
    private void selectStatement() {
        match(TokenType.SELECT);

        if (currentToken.getType() == TokenType.DISTINCT) {
            match(TokenType.DISTINCT);
        }

        selectList();
        match(TokenType.FROM);
        table();

        while (currentToken.getType() == TokenType.COMMA) {
            match(TokenType.COMMA);
            table();
        }

        if (currentToken.getType() == TokenType.WHERE) {
            match(TokenType.WHERE);
            condition();
        }

        if (currentToken.getType() == TokenType.ORDER) {
            match(TokenType.ORDER);
            match(TokenType.BY);
            orderByList();
        }

        match(TokenType.SEMICOLON);
    }

    // Producción: '*' | (column (',' column)*)
    private void selectList() {
        if (currentToken.getType() == TokenType.ASTERISK) {
            match(TokenType.ASTERISK);
        } else {
            column();

            while (currentToken.getType() == TokenType.COMMA) {
                match(TokenType.COMMA);
                column();
            }
        }
    }

    // Producción: IDENTIFIER (IDENTIFIER)?
    private void table() {
        match(TokenType.IDENTIFIER);

        if (currentToken.getType() == TokenType.IDENTIFIER) {
            match(TokenType.IDENTIFIER);
        }
    }

    // Producción: column comparisonOperator literal
    private void condition() {
        column();
        comparisonOperator();
        literal();
    }

    // Producción: IDENTIFIER ('.' IDENTIFIER)?
    private void column() {
        match(TokenType.IDENTIFIER);

        if (currentToken.getType() == TokenType.DOT) {
            match(TokenType.DOT);
            match(TokenType.IDENTIFIER);
        }
    }

    // Producción: '='
    private void comparisonOperator() {
        match(TokenType.EQUALS);
    }

    // Producción: STRING_LITERAL
    private void literal() {
        match(TokenType.STRING_LITERAL);
    }

    // Producción: column (',' column)*
    private void orderByList() {
        column();

        while (currentToken.getType() == TokenType.COMMA) {
            match(TokenType.COMMA);
            column();
        }
    }

    // Método auxiliar para verificar que el tipo de token actual coincida con el tipo esperado
    private void match(TokenType expectedType) {
        if (currentToken.getType() == expectedType) {
            consumeToken();
        } else {
            throw new SyntaxErrorException("Se esperaba " + expectedType + ", se encontró " + currentToken.getType());
        }
    }

    // Método auxiliar para avanzar al siguiente token
    private void consumeToken() {
        currentTokenIndex++;
        if (currentTokenIndex < tokens.size()) {
            currentToken = tokens.get(currentTokenIndex);
        }
    }
}
